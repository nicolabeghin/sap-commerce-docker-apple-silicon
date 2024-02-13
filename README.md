# sap-commerce-docker-apple-silicon
Run [SAP (Hybris) Commerce Cloud (TM)](https://www.sap.com/products/crm/commerce-cloud.html) on Docker on Apple Silicon (M1/M2), by leveraging the Linux `aarch64` architecture in place of x86_64, extremely slow when on Docker on Silicon.

## Related repositories
[SAP (Hybris) Commerce Cloud (TM)](https://www.sap.com/products/crm/commerce-cloud.html) on Apple Silicon (M1/M2) **natively**, without Rosetta emulation for maximum performances  https://github.com/nicolabeghin/sap-commerce-apple-silicon

## Project without dependency on `sapcorejco`
Project without requirement to connect to SAP ECC/S4 RFC (JCO): you don't need to perform anything special and can use directly the [SAPMachine 17 Docker image](https://hub.docker.com/_/sapmachine)

## Project with `sapcorejco` dependency
Project with requirement to connect to SAP ECC/S4 RFC (JCO). In june 2023 [JCO 3.1.8](https://me.sap.com/notes/3347894) has been released with Linux AARCH64 (ARM64) support. In order to leverage this: when building your image on top of [SAPMachine 17 Docker image](https://hub.docker.com/_/sapmachine) make sure to add the following in your `Dockerfile`, after downloading [sapjco31P_8-70007911.zip](https://softwaredownloads.sap.com/file/0020000000874152023)

    # add JCO libs for Linux aarch64
    COPY sapjco31P_8-70007911.zip /opt/jco.zip
    RUN       mkdir -p /opt/hybris/hybris/bin/modules/sap-framework-core/sapcorejco/lib/linuxaarch64 &&\
    	  unzip /opt/jco.zip -d /tmp &&\
    	  mkdir /tmp/jco &&\
    	  tar zxf /tmp/sapjco3-linuxaarch64-3.1.8.tgz -C /tmp/jco &&\
    	  cp -r /tmp/jco/* /opt/hybris/hybris/bin/modules/sap-framework-core/sapcorejco/lib/linuxaarch64/ &&\
    	  ls -al /opt/hybris/hybris/bin/modules/sap-framework-core/sapcorejco/lib/linuxaarch64/

Also make sure to enable Tanuki wrapper for Linux AARCH64

    # enable Tanuki wrapper for Linux aarch64
    RUN        cp /opt/hybris/hybris/bin/platform/tomcat/bin/wrapper-linux-arm-64 /opt/hybris/hybris/bin/platform/tomcat/bin/wrapper-linux-aarch64- &&\
    	   chmod +x /opt/hybris/hybris/bin/platform/tomcat/bin/wrapper-linux-arm-64 &&\
    	   chmod +x /opt/hybris/hybris/bin/platform/tomcat/bin/wrapper-linux-aarch64-

## Performance benchmark
MacOS `x86_64` (emulated through Rosetta)

> Server startup in 240040 ms

native (ref. https://github.com/nicolabeghin/sap-commerce-apple-silicon)

> Server startup in 50872 ms

Docker (ref. https://github.com/nicolabeghin/sap-commerce-docker-apple-silicon)

> Server startup in 93176 ms
