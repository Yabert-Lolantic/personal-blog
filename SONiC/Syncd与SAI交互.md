Syncd与SAI的交互通过vendorsai封装。vendorsai的初始化在Syncd的构造函数中，调用vendorSai→initialize完成初始化。
在vendorSai的构造函数中，将SAI的一系列全局接口映射到Syncd的全局api对象m_globalApis上。(构造函数中只是做了赋值，具体的链接动作应该是perl的脚本做的，过程没找到)
vendor构造函数->
VendorSai::VendorSai()
{
    SWSS_LOG_ENTER();
 
    m_apiInitialized = false;
 
    memset(&m_apis, 0, sizeof(m_apis));
 
    sai_global_apis_t ga =
    {
        .api_initialize = &sai_api_initialize,
        .api_query = &sai_api_query,
        .api_uninitialize = &sai_api_uninitialize,
        .bulk_get_attribute = nullptr,
#ifdef HAVE_SAI_BULK_OBJECT_CLEAR_STATS
        .bulk_object_clear_stats = &sai_bulk_object_clear_stats,
#else
        .bulk_object_clear_stats = nullptr,
#endif
#ifdef HAVE_SAI_BULK_OBJECT_GET_STATS
        .bulk_object_get_stats = &sai_bulk_object_get_stats,
#else
        .bulk_object_get_stats = nullptr,
#endif
        .dbg_generate_dump = nullptr,
        .get_maximum_attribute_count = nullptr,
        .get_object_count = nullptr,
        .get_object_key = nullptr,
        .log_set = &sai_log_set,
        .object_type_get_availability = &sai_object_type_get_availability,
        .object_type_query = &sai_object_type_query,
        .query_api_version = &sai_query_api_version,
        .query_attribute_capability = &sai_query_attribute_capability,
        .query_attribute_enum_values_capability = &sai_query_attribute_enum_values_capability,
        .query_object_stage = nullptr,
        .query_stats_capability = &sai_query_stats_capability,
        .switch_id_query = &sai_switch_id_query,
        .tam_telemetry_get_data = nullptr,
    };
 
    m_globalApis = ga;
}

libsai.so动态库链接->
/* SAI global API query */
 
int sai_metadata_global_apis_query(
        _Inout_ sai_global_apis_t* global_apis,
        _In_ void* handle,
        _In_ const sai_dlsym_fn dlsym,
        _In_ const sai_dlerror_fn dlerror)
{
    char* error;
    dlerror();
    global_apis->api_initialize = 0;
    global_apis->api_query = 0;
    global_apis->api_uninitialize = 0;
    global_apis->bulk_get_attribute = 0;
    global_apis->bulk_object_clear_stats = 0;
    global_apis->bulk_object_get_stats = 0;
    global_apis->dbg_generate_dump = 0;
    global_apis->get_maximum_attribute_count = 0;
    global_apis->get_object_count = 0;
    global_apis->get_object_key = 0;
    global_apis->log_set = 0;
    global_apis->object_type_get_availability = 0;
    global_apis->object_type_query = 0;
    global_apis->query_api_version = 0;
    global_apis->query_attribute_capability = 0;
    global_apis->query_attribute_enum_values_capability = 0;
    global_apis->query_object_stage = 0;
    global_apis->query_stats_capability = 0;
    global_apis->switch_id_query = 0;
    global_apis->tam_telemetry_get_data = 0;
    *(void **) (&global_apis->api_initialize) = dlsym(handle, "sai_api_initialize");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_api_initialize: %s", error);
    }
    *(void **) (&global_apis->api_query) = dlsym(handle, "sai_api_query");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_api_query: %s", error);
    }
    *(void **) (&global_apis->api_uninitialize) = dlsym(handle, "sai_api_uninitialize");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_api_uninitialize: %s", error);
    }
    *(void **) (&global_apis->bulk_get_attribute) = dlsym(handle, "sai_bulk_get_attribute");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_bulk_get_attribute: %s", error);
    }
    *(void **) (&global_apis->bulk_object_clear_stats) = dlsym(handle, "sai_bulk_object_clear_stats");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_bulk_object_clear_stats: %s", error);
    }
    *(void **) (&global_apis->bulk_object_get_stats) = dlsym(handle, "sai_bulk_object_get_stats");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_bulk_object_get_stats: %s", error);
    }
    *(void **) (&global_apis->dbg_generate_dump) = dlsym(handle, "sai_dbg_generate_dump");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_dbg_generate_dump: %s", error);
    }
    *(void **) (&global_apis->get_maximum_attribute_count) = dlsym(handle, "sai_get_maximum_attribute_count");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_get_maximum_attribute_count: %s", error);
    }
    *(void **) (&global_apis->get_object_count) = dlsym(handle, "sai_get_object_count");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_get_object_count: %s", error);
    }
    *(void **) (&global_apis->get_object_key) = dlsym(handle, "sai_get_object_key");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_get_object_key: %s", error);
    }
    *(void **) (&global_apis->log_set) = dlsym(handle, "sai_log_set");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_log_set: %s", error);
    }
    *(void **) (&global_apis->object_type_get_availability) = dlsym(handle, "sai_object_type_get_availability");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_object_type_get_availability: %s", error);
    }
    *(void **) (&global_apis->object_type_query) = dlsym(handle, "sai_object_type_query");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_object_type_query: %s", error);
    }
    *(void **) (&global_apis->query_api_version) = dlsym(handle, "sai_query_api_version");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_query_api_version: %s", error);
    }
    *(void **) (&global_apis->query_attribute_capability) = dlsym(handle, "sai_query_attribute_capability");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_query_attribute_capability: %s", error);
    }
    *(void **) (&global_apis->query_attribute_enum_values_capability) = dlsym(handle, "sai_query_attribute_enum_values_capability");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_query_attribute_enum_values_capability: %s", error);
    }
    *(void **) (&global_apis->query_object_stage) = dlsym(handle, "sai_query_object_stage");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_query_object_stage: %s", error);
    }
    *(void **) (&global_apis->query_stats_capability) = dlsym(handle, "sai_query_stats_capability");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_query_stats_capability: %s", error);
    }
    *(void **) (&global_apis->switch_id_query) = dlsym(handle, "sai_switch_id_query");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_switch_id_query: %s", error);
    }
    *(void **) (&global_apis->tam_telemetry_get_data) = dlsym(handle, "sai_tam_telemetry_get_data");
    if ((error = dlerror()) != NULL)
    {
        SAI_META_LOG_NOTICE("dlsym failed on sai_tam_telemetry_get_data: %s", error);
    }
    return SAI_STATUS_SUCCESS;
}

同swss与asic db的交互相同，vendor的initialize也是获取SAI各组件的api地址。
上述src/sonic-sairedis/SAI/meta/saimetadata.c的int sai_metadata_global_apis_query中加载SAI的动态库libsai.so
vendor调用SAI的sai_api_initialize(位于plugins/sai/SAI/xpSai/xpSai.c)将各组件的api地址放入缓存xpSaiApiTableArr中
随后通过传入SAI的api query接口(sai_api_query位于plugins/sai/SAI/xpSai/xpSai.c)给int sai_metadata_apis_query将各组件的api地址从xpSaiApiTableArr取出，放入sai_api_t m_api中。
sai_apis_t(位于src/sonic-sairedis/SAI/meta/saimetadata.h)以所有组件为成员，以此保存所有组件API接口
sai_apis_t->
typedef struct _sai_apis_t {
    sai_switch_api_t* switch_api;
    sai_port_api_t* port_api;
    sai_fdb_api_t* fdb_api;
    sai_vlan_api_t* vlan_api;
    sai_virtual_router_api_t* virtual_router_api;
    sai_route_api_t* route_api;
    sai_next_hop_api_t* next_hop_api;
    sai_next_hop_group_api_t* next_hop_group_api;
    sai_router_interface_api_t* router_interface_api;
    sai_neighbor_api_t* neighbor_api;
    sai_acl_api_t* acl_api;
    sai_hostif_api_t* hostif_api;
    sai_mirror_api_t* mirror_api;
    sai_samplepacket_api_t* samplepacket_api;
    sai_stp_api_t* stp_api;
    sai_lag_api_t* lag_api;
    sai_policer_api_t* policer_api;
    sai_wred_api_t* wred_api;
    sai_qos_map_api_t* qos_map_api;
    sai_queue_api_t* queue_api;
    sai_scheduler_api_t* scheduler_api;
    sai_scheduler_group_api_t* scheduler_group_api;
    sai_buffer_api_t* buffer_api;
    sai_hash_api_t* hash_api;
    sai_udf_api_t* udf_api;
    sai_tunnel_api_t* tunnel_api;
    sai_l2mc_api_t* l2mc_api;
    sai_ipmc_api_t* ipmc_api;
    sai_rpf_group_api_t* rpf_group_api;
    sai_l2mc_group_api_t* l2mc_group_api;
    sai_ipmc_group_api_t* ipmc_group_api;
    sai_mcast_fdb_api_t* mcast_fdb_api;
    sai_bridge_api_t* bridge_api;
    sai_tam_api_t* tam_api;
    sai_srv6_api_t* srv6_api;
    sai_mpls_api_t* mpls_api;
    sai_dtel_api_t* dtel_api;
    sai_bfd_api_t* bfd_api;
    sai_isolation_group_api_t* isolation_group_api;
    sai_nat_api_t* nat_api;
    sai_counter_api_t* counter_api;
    sai_debug_counter_api_t* debug_counter_api;
    sai_macsec_api_t* macsec_api;
    sai_system_port_api_t* system_port_api;
    sai_my_mac_api_t* my_mac_api;
    sai_ipsec_api_t* ipsec_api;
    sai_generic_programmable_api_t* generic_programmable_api;
    sai_ars_api_t* ars_api;
    sai_ars_profile_api_t* ars_profile_api;
    sai_twamp_api_t* twamp_api;
    sai_poe_api_t* poe_api;
    sai_bmtor_api_t* bmtor_api;
    sai_dash_acl_api_t* dash_acl_api;
    sai_dash_direction_lookup_api_t* dash_direction_lookup_api;
    sai_dash_eni_api_t* dash_eni_api;
    sai_dash_inbound_routing_api_t* dash_inbound_routing_api;
    sai_dash_meter_api_t* dash_meter_api;
    sai_dash_outbound_ca_to_pa_api_t* dash_outbound_ca_to_pa_api;
    sai_dash_outbound_routing_api_t* dash_outbound_routing_api;
    sai_dash_vnet_api_t* dash_vnet_api;
    sai_dash_pa_validation_api_t* dash_pa_validation_api;
    sai_dash_vip_api_t* dash_vip_api;
} sai_apis_t;

Syncd对SAI接口的调用与SWSS相同
同样需要区分是否存在oid与entry。不同于SWSS可以通过调用的api接口从一开始就区分oid与entry(例如port创建时调用的sai_port_api->create_port，fdb创建时调用的sai_fdb_api→create_fdb_entry，携带的参数数目也不相同)
Syncd接收到配置后，通过组件类型获取到存放在sai_metadata_all_object_type_infos中的组件相关信息
Object infos table->
const sai_object_type_info_t* const sai_metadata_all_object_type_infos[] = {
    NULL,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_PORT,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_LAG,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_VIRTUAL_ROUTER,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_NEXT_HOP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_NEXT_HOP_GROUP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_ROUTER_INTERFACE,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_ACL_TABLE,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_ACL_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_ACL_COUNTER,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_ACL_RANGE,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_ACL_TABLE_GROUP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_ACL_TABLE_GROUP_MEMBER,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_HOSTIF,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_MIRROR_SESSION,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_SAMPLEPACKET,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_STP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_HOSTIF_TRAP_GROUP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_POLICER,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_WRED,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_QOS_MAP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_QUEUE,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_SCHEDULER,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_SCHEDULER_GROUP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_BUFFER_POOL,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_BUFFER_PROFILE,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_INGRESS_PRIORITY_GROUP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_LAG_MEMBER,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_HASH,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_UDF,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_UDF_MATCH,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_UDF_GROUP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_FDB_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_SWITCH,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_HOSTIF_TRAP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_HOSTIF_TABLE_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_NEIGHBOR_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_ROUTE_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_VLAN,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_VLAN_MEMBER,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_HOSTIF_PACKET,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TUNNEL_MAP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TUNNEL,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TUNNEL_TERM_TABLE_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_FDB_FLUSH,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_NEXT_HOP_GROUP_MEMBER,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_STP_PORT,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_RPF_GROUP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_RPF_GROUP_MEMBER,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_L2MC_GROUP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_L2MC_GROUP_MEMBER,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_IPMC_GROUP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_IPMC_GROUP_MEMBER,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_L2MC_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_IPMC_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_MCAST_FDB_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_HOSTIF_USER_DEFINED_TRAP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_BRIDGE,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_BRIDGE_PORT,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TUNNEL_MAP_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TAM,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_SRV6_SIDLIST,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_PORT_POOL,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_INSEG_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_DTEL,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_DTEL_QUEUE_REPORT,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_DTEL_INT_SESSION,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_DTEL_REPORT_SESSION,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_DTEL_EVENT,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_BFD_SESSION,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_ISOLATION_GROUP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_ISOLATION_GROUP_MEMBER,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TAM_MATH_FUNC,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TAM_REPORT,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TAM_EVENT_THRESHOLD,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TAM_TEL_TYPE,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TAM_TRANSPORT,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TAM_TELEMETRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TAM_COLLECTOR,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TAM_EVENT_ACTION,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TAM_EVENT,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_NAT_ZONE_COUNTER,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_NAT_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TAM_INT,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_COUNTER,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_DEBUG_COUNTER,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_PORT_CONNECTOR,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_PORT_SERDES,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_MACSEC,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_MACSEC_PORT,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_MACSEC_FLOW,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_MACSEC_SC,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_MACSEC_SA,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_SYSTEM_PORT,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_FINE_GRAINED_HASH_FIELD,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_SWITCH_TUNNEL,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_MY_SID_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_MY_MAC,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_NEXT_HOP_GROUP_MAP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_IPSEC,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_IPSEC_PORT,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_IPSEC_SA,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_GENERIC_PROGRAMMABLE,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_ARS_PROFILE,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_ARS,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_ACL_TABLE_CHAIN_GROUP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TWAMP_SESSION,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TAM_COUNTER_SUBSCRIPTION,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_POE_DEVICE,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_POE_PSE,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_POE_PORT,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TABLE_BITMAP_CLASSIFICATION_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TABLE_BITMAP_ROUTER_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_TABLE_META_TUNNEL_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_DASH_ACL_GROUP,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_DASH_ACL_RULE,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_DIRECTION_LOOKUP_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_ENI_ETHER_ADDRESS_MAP_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_ENI,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_INBOUND_ROUTING_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_METER_BUCKET,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_METER_POLICY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_METER_RULE,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_OUTBOUND_CA_TO_PA_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_OUTBOUND_ROUTING_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_VNET,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_PA_VALIDATION_ENTRY,
    &sai_metadata_object_type_info_SAI_OBJECT_TYPE_VIP_ENTRY,
    NULL
};

以port和fdb为例，.isnonobjectid字段携带了该组件是否是存在oid参数
port&fdb->
const sai_object_type_info_t sai_metadata_object_type_info_SAI_OBJECT_TYPE_PORT = {
    .objecttype           = (sai_object_type_t)SAI_OBJECT_TYPE_PORT,
    .objecttypename       = "SAI_OBJECT_TYPE_PORT",
    .attridstart          = SAI_PORT_ATTR_START,
    .attridend            = SAI_PORT_ATTR_END,
    .enummetadata         = &sai_metadata_enum_sai_port_attr_t,
    .attrmetadata         = sai_metadata_object_type_sai_port_attr_t,
    .attrmetadatalength   = 178,
    .isnonobjectid        = false,
    .isobjectid           = !false,
    .structmembers        = NULL,
    .structmemberscount   = 0,
    .revgraphmembers      = sai_metadata_SAI_OBJECT_TYPE_PORT_rev_graph_members,
    .revgraphmemberscount = 48,
    .create               = sai_metadata_generic_create_SAI_OBJECT_TYPE_PORT,
    .remove               = sai_metadata_generic_remove_SAI_OBJECT_TYPE_PORT,
    .set                  = sai_metadata_generic_set_SAI_OBJECT_TYPE_PORT,
    .get                  = sai_metadata_generic_get_SAI_OBJECT_TYPE_PORT,
    .getstats             = sai_metadata_generic_get_stats_SAI_OBJECT_TYPE_PORT,
    .getstatsext          = sai_metadata_generic_get_stats_ext_SAI_OBJECT_TYPE_PORT,
    .clearstats           = sai_metadata_generic_clear_stats_SAI_OBJECT_TYPE_PORT,
    .isexperimental       = false,
    .statenum             = &sai_metadata_enum_sai_port_stat_t,
};
 
const sai_object_type_info_t sai_metadata_object_type_info_SAI_OBJECT_TYPE_FDB_ENTRY = {
    .objecttype           = (sai_object_type_t)SAI_OBJECT_TYPE_FDB_ENTRY,
    .objecttypename       = "SAI_OBJECT_TYPE_FDB_ENTRY",
    .attridstart          = SAI_FDB_ENTRY_ATTR_START,
    .attridend            = SAI_FDB_ENTRY_ATTR_END,
    .enummetadata         = &sai_metadata_enum_sai_fdb_entry_attr_t,
    .attrmetadata         = sai_metadata_object_type_sai_fdb_entry_attr_t,
    .attrmetadatalength   = 8,
    .isnonobjectid        = true,
    .isobjectid           = !true,
    .structmembers        = sai_metadata_struct_members_sai_fdb_entry_t,
    .structmemberscount   = 3,
    .revgraphmembers      = NULL,
    .revgraphmemberscount = 0,
    .create               = sai_metadata_generic_create_SAI_OBJECT_TYPE_FDB_ENTRY,
    .remove               = sai_metadata_generic_remove_SAI_OBJECT_TYPE_FDB_ENTRY,
    .set                  = sai_metadata_generic_set_SAI_OBJECT_TYPE_FDB_ENTRY,
    .get                  = sai_metadata_generic_get_SAI_OBJECT_TYPE_FDB_ENTRY,
    .getstats             = sai_metadata_generic_get_stats_SAI_OBJECT_TYPE_FDB_ENTRY,
    .getstatsext          = sai_metadata_generic_get_stats_ext_SAI_OBJECT_TYPE_FDB_ENTRY,
    .clearstats           = sai_metadata_generic_clear_stats_SAI_OBJECT_TYPE_FDB_ENTRY,
    .isexperimental       = false,
    .statenum             = NULL,
};

存在oid的组件通过sai_status_t Syncd::processOid调用SAI接口，实际上通过上述SAI初始化过程中保存在m_apis中的SAI接口下发配置
oid sai create->
sai_status_t VendorSai::create(
        _In_ sai_object_type_t objectType,
        _Out_ sai_object_id_t* objectId,
        _In_ sai_object_id_t switchId,
        _In_ uint32_t attr_count,
        _In_ const sai_attribute_t *attr_list)
{
    MUTEX();
    SWSS_LOG_ENTER();
    VENDOR_CHECK_API_INITIALIZED();
 
    sai_object_meta_key_t mk = { .objecttype = objectType, .objectkey = { .key = { .object_id = 0 } } };
 
    auto status = sai_metadata_generic_create(&m_apis, &mk, switchId, attr_count, attr_list);
 
    if (status == SAI_STATUS_SUCCESS)
    {
        *objectId = mk.objectkey.key.object_id;
    }
 
    return status;
}
 
/* Generic Quad API */
 
sai_status_t sai_metadata_generic_create(
        _In_ const sai_apis_t* apis,
        _Inout_ sai_object_meta_key_t *meta_key,
        _In_ sai_object_id_t switch_id,
        _In_ uint32_t attr_count,
        _In_ const sai_attribute_t *attr_list)
{
    switch((int)meta_key->objecttype)
    {
        case SAI_OBJECT_TYPE_NULL:
            return SAI_STATUS_NOT_SUPPORTED;
        case SAI_OBJECT_TYPE_PORT:
            return (apis->port_api && apis->port_api->create_port)
                ? apis->port_api->create_port(&meta_key->objectkey.key.object_id, switch_id, attr_count, attr_list)
                : SAI_STATUS_NOT_IMPLEMENTED;
        case SAI_OBJECT_TYPE_LAG:
            return (apis->lag_api && apis->lag_api->create_lag)
                ? apis->lag_api->create_lag(&meta_key->objectkey.key.object_id, switch_id, attr_count, attr_list)
                : SAI_STATUS_NOT_IMPLEMENTED;
    ...
}

不存在oid的entry通过sai_status_t Syncd::processEntry直接调用sai query时获取到的SAI接口
sai entry create->
#define DECLARE_CREATE_ENTRY(OT,ot)                                         \
sai_status_t VendorSai::create(                                             \
        _In_ const sai_ ## ot ## _t* entry,                                 \
        _In_ uint32_t attr_count,                                           \
        _In_ const sai_attribute_t *attr_list)                              \
{                                                                           \
    MUTEX();                                                                \
    SWSS_LOG_ENTER();                                                       \
    VENDOR_CHECK_API_INITIALIZED();                                         \
    auto info = sai_metadata_get_object_type_info(                          \
        (sai_object_type_t)SAI_OBJECT_TYPE_ ## OT);                         \
    sai_object_meta_key_t mk = { .objecttype = info->objecttype,            \
        .objectkey = { .key = { .ot = *entry } } };                         \
    return info->create(&mk, 0, attr_count, attr_list);                     \
}
 
SAIREDIS_DECLARE_EVERY_ENTRY(DECLARE_CREATE_ENTRY);
 
const sai_object_type_info_t sai_metadata_object_type_info_SAI_OBJECT_TYPE_FDB_ENTRY = {
    .objecttype           = (sai_object_type_t)SAI_OBJECT_TYPE_FDB_ENTRY,
    .objecttypename       = "SAI_OBJECT_TYPE_FDB_ENTRY",
    .attridstart          = SAI_FDB_ENTRY_ATTR_START,
    .attridend            = SAI_FDB_ENTRY_ATTR_END,
    .enummetadata         = &sai_metadata_enum_sai_fdb_entry_attr_t,
    .attrmetadata         = sai_metadata_object_type_sai_fdb_entry_attr_t,
    .attrmetadatalength   = 8,
    .isnonobjectid        = true,
    .isobjectid           = !true,
    .structmembers        = sai_metadata_struct_members_sai_fdb_entry_t,
    .structmemberscount   = 3,
    .revgraphmembers      = NULL,
    .revgraphmemberscount = 0,
    .create               = sai_metadata_generic_create_SAI_OBJECT_TYPE_FDB_ENTRY,
    .remove               = sai_metadata_generic_remove_SAI_OBJECT_TYPE_FDB_ENTRY,
    .set                  = sai_metadata_generic_set_SAI_OBJECT_TYPE_FDB_ENTRY,
    .get                  = sai_metadata_generic_get_SAI_OBJECT_TYPE_FDB_ENTRY,
    .getstats             = sai_metadata_generic_get_stats_SAI_OBJECT_TYPE_FDB_ENTRY,
    .getstatsext          = sai_metadata_generic_get_stats_ext_SAI_OBJECT_TYPE_FDB_ENTRY,
    .clearstats           = sai_metadata_generic_clear_stats_SAI_OBJECT_TYPE_FDB_ENTRY,
    .isexperimental       = false,
    .statenum             = NULL,
};
 
sai_status_t sai_metadata_generic_create_SAI_OBJECT_TYPE_FDB_ENTRY(
    _Inout_ sai_object_meta_key_t *meta_key,
    _In_ sai_object_id_t switch_id,
    _In_ uint32_t attr_count,
    _In_ const sai_attribute_t *attr_list)
{
    return sai_metadata_sai_fdb_api->create_fdb_entry(&meta_key->objectkey.key.fdb_entry, attr_count, attr_list);
}
 
/* SAI API query */
 
int sai_metadata_apis_query(
    _In_ const sai_api_query_fn api_query,
    _Inout_ sai_apis_t *apis)
{
    ...
 
    status = api_query(SAI_API_FDB, (void**)&sai_metadata_sai_fdb_api);
    apis->fdb_api = sai_metadata_sai_fdb_api;
    if (status != SAI_STATUS_SUCCESS)
    {
        count++;
        const char *name = sai_metadata_get_enum_value_name(&sai_metadata_enum_sai_status_t, status);
        SAI_META_LOG_NOTICE("failed to query api SAI_API_FDB: %s (%d)", name, status);
    }
 
    ...
}





























