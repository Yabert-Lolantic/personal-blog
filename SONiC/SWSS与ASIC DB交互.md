SWSS通过sairedis与ASIC db通信。
sairedis的初始化在SWSS的initSaiApi()函数中，在SWSS的主函数main中被调用。
swss-main->
int main(int argc, char **argv)
{
    swss::Logger::linkToDbNative("orchagent");
 
    SWSS_LOG_ENTER();
 
    WarmStart::initialize("orchagent", "swss");
    WarmStart::checkWarmStart("orchagent", "swss");
 
    ...
 
    /* Initialize sairedis */
    initSaiApi();
 
    ...
 
}

initSaiApi()位于saihelper.cpp，完成各组件业务API的地址获取，以及配置各组件的日志等级。
sai_api_initialize初始化操作ASIC DB的create/remove/set_attribute/get_attribute等接口，初步封装在redis_sai的ClientServerSai对象中。
sai_api_query用于获取对应组件操作数据库的指针。
sai_api_query第一个参数为组件类型，通过组件类型的枚举值拿到redis_apis中存放的组件api地址，地址放入sai_api_query的第二个参数。
redis_apis中存放的api地址通过名为API的宏导入，API的定义在文件src/sonic-sairedis/lib/sai_redis_interfacequery.cpp中，如下代码：
宏API->
#define API(api) .api ## _api = const_cast<sai_ ## api ## _api_t*>(&redis_ ## api ## _api)

以port为例，port组件的api接口位于const sai_port_api_t redis_port_api中。具体存在的位置在sai_redis_ ## api ##.cpp中，port的在sai_redis_port.cpp中。
sai_redis_port.cpp中的接口通过一系列宏组织，展开后即port组件的所有接口。其他组件的接口类似。
port组件部分宏展开后如下，调用上述sai_api_initialize中初始化的ClientServerSai对象redis_sai。
port接口代码->
#include "sai_redis.h"
 
static sai_status_t redis_clear_port_all_stats(
        _In_ sai_object_id_t port_id)
{
    SWSS_LOG_ENTER();
 
    return SAI_STATUS_NOT_IMPLEMENTED;
}
 
static sai_status_t redis_create_port(           
            _Out_ sai_object_id_t *object_id,          
            _In_ sai_object_id_t switch_id,            
            _In_ uint32_t attr_count,                  
            _In_ const sai_attribute_t *attr_list)     
{                                                      
    SWSS_LOG_ENTER();                                  
    return redis_sai->create(                          
            (sai_object_type_t)SAI_OBJECT_TYPE_PORT, 
            object_id,                                 
            switch_id,                                 
            attr_count,                                
            attr_list);                                
}
           
static sai_status_t redis_remove_port(           
            _In_ sai_object_id_t object_id)            
{                                                      
    SWSS_LOG_ENTER();                                  
    return redis_sai->remove(                          
            (sai_object_type_t)SAI_OBJECT_TYPE_PORT, 
            object_id);                                
}
       
static sai_status_t redis_set_port_attribute(
            _In_ sai_object_id_t object_id,            
            _In_ const sai_attribute_t *attr)          
{                                                      
    SWSS_LOG_ENTER();                                  
    return redis_sai->set(                             
            (sai_object_type_t)SAI_OBJECT_TYPE_PORT, 
            object_id,                                 
            attr);                                     
}
              
 
static sai_status_t redis_get_port_attribute(
            _In_ sai_object_id_t object_id,            
            _In_ uint32_t attr_count,                  
            _Inout_ sai_attribute_t *attr_list)        
{                                                      
    SWSS_LOG_ENTER();                                  
    return redis_sai->get(                             
            (sai_object_type_t)SAI_OBJECT_TYPE_PORT, 
            object_id,                                 
            attr_count,                                
            attr_list);                                
}
 
const sai_port_api_t redis_port_api = {
    redis_create_port,               
    redis_remove_port,               
    redis_set_port_attribute,     
    redis_get_port_attribute,
    redis_clear_port_all_stats,
};

ClientServerSai对象redis_sai定义在src/sonic-sairedis/lib/sai_redis_interfacequery.cpp文件开始位置。
redis_sai只是外部的一层封装，内部还经过ServerSai、Sai、Context、RedisRemoteSaiInterface的封装。
ServerSai：src/sonic-sairedis/lib/ServerSai.cpp
Sai：src/sonic-sairedis/lib/Sai.cpp
Context：src/sonic-sairedis/lib/Context.cpp
RedisRemoteSaiInterface：src/sonic-sairedis/lib/RedisRemoteSaiInterface.cpp
对于存在OID的组件，比如port组件，在构造ASIC DB数据库key时，将组件类型与分配的OID共同构建为数据库表项的key
oid create->
sai_status_t RedisRemoteSaiInterface::create(
        _In_ sai_object_type_t objectType,
        _Out_ sai_object_id_t* objectId,
        _In_ sai_object_id_t switchId,
        _In_ uint32_t attr_count,
        _In_ const sai_attribute_t *attr_list)
{
    SWSS_LOG_ENTER();
 
    *objectId = SAI_NULL_OBJECT_ID;
 
    if (objectType == SAI_OBJECT_TYPE_SWITCH)
    {
        // for given hardware info we always return same switch id,
        // this is required since we could be performing warm boot here
 
        auto hwinfo = Globals::getHardwareInfo(attr_count, attr_list);
 
        if (hwinfo.size())
        {
            m_recorder->recordComment("SAI_SWITCH_ATTR_SWITCH_HARDWARE_INFO=" + hwinfo);
        }
 
        switchId = m_virtualObjectIdManager->allocateNewSwitchObjectId(hwinfo);
 
        *objectId = switchId;
 
        if (switchId == SAI_NULL_OBJECT_ID)
        {
            SWSS_LOG_ERROR("switch ID allocation failed");
 
            return SAI_STATUS_FAILURE;
        }
 
        auto *attr = sai_metadata_get_attr_by_id(
                SAI_SWITCH_ATTR_INIT_SWITCH,
                attr_count,
                attr_list);
 
        if (attr && attr->value.booldata == false)
        {
            if (m_switchContainer->contains(*objectId))
            {
                SWSS_LOG_NOTICE("switch container already contains switch %s",
                        sai_serialize_object_id(*objectId).c_str());
 
                return SAI_STATUS_SUCCESS;
            }
 
            refreshTableDump();
 
            if (m_tableDump.find(switchId) == m_tableDump.end())
            {
                SWSS_LOG_ERROR("failed to find switch %s to connect (init=false)",
                        sai_serialize_object_id(switchId).c_str());
 
                m_virtualObjectIdManager->releaseObjectId(switchId);
 
                return SAI_STATUS_FAILURE;
            }
 
            // when init is false, don't send query to syncd, just return success
            // that we found switch and we connected to it
 
            auto sw = std::make_shared<Switch>(*objectId, attr_count, attr_list);
 
            m_switchContainer->insert(sw);
 
            return SAI_STATUS_SUCCESS;
        }
    }
    else
    {
        *objectId = m_virtualObjectIdManager->allocateNewObjectId(objectType, switchId);
    }
 
    if (*objectId == SAI_NULL_OBJECT_ID)
    {
        SWSS_LOG_ERROR("failed to create %s, with switch id: %s",
                sai_serialize_object_type(objectType).c_str(),
                sai_serialize_object_id(switchId).c_str());
 
        return SAI_STATUS_INSUFFICIENT_RESOURCES;
    }
 
    // NOTE: objectId was allocated by the caller
 
    auto status = create(
            objectType,
            sai_serialize_object_id(*objectId),
            attr_count,
            attr_list);
 
    if (objectType == SAI_OBJECT_TYPE_SWITCH && status == SAI_STATUS_SUCCESS)
    {
        /*
         * When doing CREATE operation user may want to update notification
         * pointers, since notifications can be defined per switch we need to
         * update them.
         *
         * TODO: should be moved inside to redis_generic_create
         */
 
        auto sw = std::make_shared<Switch>(*objectId, attr_count, attr_list);
 
        m_switchContainer->insert(sw);
    }
    else if (status != SAI_STATUS_SUCCESS)
    {
        // if create failed, then release allocated object
        m_virtualObjectIdManager->releaseObjectId(*objectId);
    }
 
    return status;
}

对于不存在OID的组件，比如fdb组件，在构造ASIC DB数据库key时，则将fdb entry的某些参数替换OID作为key存在
no oid create->
#define DECLARE_CREATE_ENTRY(OT,ot)                             \
sai_status_t RedisRemoteSaiInterface::create(                   \
        _In_ const sai_ ## ot ## _t* ot,                        \
        _In_ uint32_t attr_count,                               \
        _In_ const sai_attribute_t *attr_list)                  \
{                                                               \
    SWSS_LOG_ENTER();                                           \
    return create(                                              \
            (sai_object_type_t)SAI_OBJECT_TYPE_ ## OT,          \
            sai_serialize_ ## ot(*ot),                          \
            attr_count,                                         \
            attr_list);                                         \
}
 
SAIREDIS_DECLARE_EVERY_ENTRY(DECLARE_CREATE_ENTRY);

/*
std::string sai_serialize_fdb_entry(
        _In_ const sai_fdb_entry_t& fdb_entry)
{
    SWSS_LOG_ENTER();
 
    json j;
 
    j["switch_id"] = sai_serialize_object_id(fdb_entry.switch_id);
    j["mac"] = sai_serialize_mac(fdb_entry.mac_address);
    j["bvid"] = sai_serialize_object_id(fdb_entry.bv_id);
 
    return j.dump();
}
*/

最后通过数据库redis Channel对象m_communicationChannel传入操作类型将表项写入ASIC DB。m_communicationChannel最终调用的通信模型中的ProducerTable对象m_asicState操作数据库。
db set->
sai_status_t RedisRemoteSaiInterface::create(
        _In_ sai_object_type_t object_type,
        _In_ const std::string& serializedObjectId,
        _In_ uint32_t attr_count,
        _In_ const sai_attribute_t *attr_list)
{
    SWSS_LOG_ENTER();
 
    auto entry = SaiAttributeList::serialize_attr_list(
            object_type,
            attr_count,
            attr_list,
            false);
 
    if (entry.empty())
    {
        // make sure that we put object into db
        // even if there are no attributes set
        swss::FieldValueTuple null("NULL", "NULL");
 
        entry.push_back(null);
    }
 
    auto serializedObjectType = sai_serialize_object_type(object_type);
 
    const std::string key = serializedObjectType + ":" + serializedObjectId;
 
    SWSS_LOG_DEBUG("generic create key: %s, fields: %" PRIu64, key.c_str(), entry.size());
 
    m_recorder->recordGenericCreate(key, entry);
 
    m_communicationChannel->set(key, entry, REDIS_ASIC_STATE_COMMAND_CREATE);
 
    auto status = waitForResponse(SAI_COMMON_API_CREATE);
 
    m_recorder->recordGenericCreateResponse(status);
 
    return status;
}