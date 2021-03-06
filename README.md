# Azure-Monitor-Workbooks

## Requirements
* F5 Telemetry Streamaing,(ver 1.10 or later) - https://clouddocs.f5.com/products/extensions/f5-telemetry-streaming/latest/
* F5 Application Services 3 Extension,(ver 3.18 or later)  - https://clouddocs.f5.com/products/extensions/f5-appsvcs-extension/latest/
* Existing Azure Workspace - Will need to supply workspace ID and primary or secondary key.

## Configuration
In a nutshell, you will apply the [AS3 declaration](https://clouddocs.f5.com/products/extensions/f5-telemetry-streaming/latest/event-listener.html#configure-logging-using-as3), (minimal.json) to configure basic TS streaming, (the sytem metrics workbook utilizes the F5Telemetry_system_CL  table) and run three TMSH commands, (shown below) to enable AVR streaming.

The system metrics workbook makes use of basic AVR telemetry, (F5Telemetry_AVR_CL)  as well as TCP and HTTP profile AVR streams.  The second two commands  create the profiles, (TCP & HTTP).  These profiles are in turn applied to virtual server(s).  If you do not have virtual servers and/or do not apply them to at least one virtual server, the workbook will show some missing info; just an FYI. 

### Direct analytics logging to the TS virtual, (previously created via AS3 declaration)
    modify analytics global-settings { offbox-protocol tcp offbox-tcp-addresses add { 127.0.0.1 } offbox-tcp-port 6514 use-offbox enabled }Create ltm profile tcp-analytics telemetry-tcp-analytics { collect-city enabled collect-continent enabled collect-country enabled collect-nexthop enabled collect-post-code enabled collect-region enabled collect-remote-host-ip enabled collect-remote-host-subnet enabled collected-by-server-side enabled }

### Create TCP logging profile
    Create ltm profile tcp-analytics telemetry-tcp-analytics { collect-city enabled collect-continent enabled collect-country enabled collect-nexthop enabled collect-post-code enabled collect-region enabled collect-remote-host-ip enabled collect-remote-host-subnet enabled collected-by-server-side enabled }

### Create HTTP logging profile 
    Create ltm profile analytics telemetry-http-analytics { collect-geo enabled collect-http-timing-metrics enabled collect-ip enabled collect-max-tps-and-throughput enabled collect-methods enabled collect-page-load-time enabled collect-response-codes enabled collect-subnets enabled collect-url enabled collect-user-agent enabled collect-user-sessions enabled publish-irule-statistics enabled }
 
### Assign profiles to; can also be applied via GUI -‘Local Traffic’-->’Virtual Servers’
    TCP - modify ltm virtual <VIRTUAL_SERVER_NAME> profiles add { telemetry-tcp-analytics { context all } }}
    HTTP -modify ltm virtual <VIRTUAL_SERVER_NAME> profiles add { telemetry-http-analytics { context all } } 
 
### Apply Telemetry Streaming Declaration
Finally, apply the [TS declaration](https://clouddocs.f5.com/products/extensions/f5-telemetry-streaming/latest/setting-up-consumer.html#azure-ref), (azstream.json) to begin streaming telemetry data to your existing Azure log analytics workspace.  You will need to provide the workspaceID and key. Below are examples of the deployed workbook.
 
 ![picture](images/f5smdark.png)  ![picture](images/f5smlight.png)  

