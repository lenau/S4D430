# S4D430

## Table App Index
```
@EndUserText.label : 'App Index'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table z25appindex {

  key client         : abap.clnt not null;
  key uuid           : sysuuid_x16 not null;
  id                 : /uif/lrep_obj_uri;
  title              : abap.string(0);
  url                : /uif/lrep_obj_uri;
  repo_name          : /uif/lrep_obj_uri;
  created_by         : abp_creation_user;
  created_at         : abp_creation_tstmpl;
  last_changed_by    : abp_lastchange_user;
  last_changed       : abp_lastchange_tstmpl;
  local_last_by      : abp_locinst_lastchange_user;
  local_last_changed : abp_locinst_lastchange_tstmpl;

}
```

## static action refreshAppIndex;
```
  METHOD refreshAppIndex.

    DATA(result) =
      /ui5/cl_ui5_app_index=>get_instance(
        )->/ui5/if_ui5_app_index_search~search(
          EXPORTING
            it_params  = VALUE #(    "( name = 'sap.fiori/registrationIds'        value = 'F4638' )
                                     ( name = 'fileType'          value = 'appdescr' )
                                     ( name = 'fileType'          value = 'appdescr_variant' )
                                     ( name = 'sap.app/type'      value = 'application' )
                                     ( name = 'sap.ui/technology' value = 'UI5' ) )
            it_requested_columns = VALUE #( ( `sap.app/id` )
                                            ( `fileType` )
                                            ( `sap.app/type` )
                                            ( `sap.ui/technology` )
                                            ( `sap.app/ach` )
                                            ( `sap.fiori/registrationIds` )
                                            ( `sap.app/title` )
                                            ( `sap.gui/transaction` )
                                            ( `url` )
                                            ( `repoName` ) ) ).

    TYPES: tt_app_index TYPE STANDARD TABLE OF z25appindex WITH DEFAULT KEY.

    DATA(app_index) =
      VALUE tt_app_index( FOR <result> IN CORRESPONDING tt_app_index( result->* )
                           ( client     = '400'
                             uuid       = cl_system_uuid=>create_uuid_x16_static( )
                             id         = <result>-id
                             title      = <result>-title
                             url        = <result>-url
                             repo_name  = <result>-repo_name
                             created_by = cl_abap_context_info=>get_user_technical_name( ) ) ).

    DELETE FROM z25appindex.

    INSERT z25appindex FROM TABLE app_index.

  ENDMETHOD.
```

