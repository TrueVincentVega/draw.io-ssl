## drpesojr/dns-bind9

* [GitHub](https://github.com/TrueVincentVega/draw.io-ssl) - open source repository of this image.

Image is based on [fjudith/draw.io](https://hub.docker.com/r/fjudith/draw.io "dockerhub"), but the main difference is that we do not use the ** .keystore ** _Java_ certificate store, but use our own generated SSL certificates.. This image build support multiple architectures such as `arm64`. 

[draw.io](https://github.com/jgraph/draw.io) (formerly Diagramly) is free online diagram software. You can use it as a flowchart maker, network diagram software, to create UML online, as an ER diagram tool, to design database schema, to build BPMN online, as a circuit diagram maker, and more. draw.io can import .vsdx, Gliffy™ and Lucidchart™ files.

## Usage

Here are some example snippets to help you get started creating a container.

### docker script

```
#!/bin/bash
docker run -d \
    --name=drawio \
    --restart=unless-stopped \
    -p 8080:8080 \
    -p 8443:8443 \
    drpesojr/drawio-ssl:latest
```

## Parameters

Container images are configured using parameters passed at runtime (such as those above). These parameters are separated by a colon and indicate external:internal respectively. For example, -p 8080:8080 would expose port 8080 from inside the container to be accessible from the host's IP on port 8080 outside the container.

| Parameter | Function |
| :----: | --- |
| `-p 80` | Application HTTP Port. |
| `-p 443` | Application HTTPS Port. |

## Configure your own SSL certificates in the container

The image [drpesojr / draw.io-ssl] (https://hub.docker.com/r/drpesojr/draw.io-ssl "dockerhub") uses already generated certificates:

| Certificate | Explanation |
| :----: | --- |
| cert.pem | certificate of the server where the docker service is installed and on which the draw.io-ssl container is awakened. |
| key.pem | the certificate key of the server where the docker service is installed and on which the draw.io-ssl container is awakened. |
| mail.pem | the root server certificate from which we generate further certificates. |

These certificates must be placed in a container in the ** / usr / local / tomcat / conf / ** directory. You also need to fix 2 Tomcat server files ** server.xml ** and ** web.xml ** in the ** / usr / local / tomcat / conf / ** directory.

It is necessary to add at the end of the file ** web.xml **:

```
<security-constraint>
    <web-resource-collection>
        <web-resource-name>securedapp</web-resource-name>
        <url-pattern>/*</url-pattern>
    </web-resource-collection>
    <user-data-constraint>
        <transport-guarantee>CONFIDENTIAL</transport-guarantee>
    </user-data-constraint>
</security-constraint>
```

In the file ** server.xml ** there are 2 variants of HTTPS configuration (1 variant - with **. Keystore ** and password to it; 2 variant - with 2h certificates and key).

We use option 2 of the volume in the file ** server.xml ** comment on the terms:

```
<!--Connector port="8443"
protocol="org.apache.coyote.http11.Http11NioProtocol"
SSLEnabled="true"
        maxThreads="150"
        scheme="https"
        secure="true"
        clientAuth="false"
        sslProtocol="TLS"
        KeystoreFile="/usr/local/tomcat/.keystore"
        KeystorePass="V3ry1nS3cur3P4ssw0rd"
    /-->
```

And we add configuration of connection on HTTPS:

```
<Connector port="8443" protocol="org.apache.coyote.http11.Http11AprProtocol"
				   maxThreads="150" SSLEnabled="true" >
			<UpgradeProtocol className="org.apache.coyote.http2.Http2Protocol" />
			<SSLHostConfig>
				<Certificate certificateKeyFile="conf/key.pem"
                         certificateFile="conf/cert.pem"
                         certificateChainFile="conf/mail.pem"
                         type="RSA" />
			</SSLHostConfig>
		</Connector>
```

P.S. Note that in the Tomcat server configuration file ** server.xml ** we do not prescribe the full path to the ** / usr / local / tomcat / conf / cert.pem ** certificates, but such ** conf / cert.pem * *.

Also verify that the ** server.xml ** file conforms to the Tomcat server architecture.

The architecture of the Tomcat server is shown in the diagram:

![ArchitectureTomcat](https://github.com/TrueVincentVega/draw.io-ssl/blob/master/TomcatArchitecture.png?raw=true "Architecture of the Tomcat server")


You can download the container from the dockerhub repository at [drpesojr / draw.io-ssl] (https://hub.docker.com/r/drpesojr/draw.io-ssl "dockerhub") and put your certificates in by replacing the old ones in the container .

Or collect an image with your certificates. All required files can be downloaded or viewed in the open-source repository at [GitHub] (https://github.com/TrueVincentVega/draw.io-ssl "TrueVincentVega"). Put your certificates in the ** certificats ** directory and in the ** server.xml ** file correct the link to SSL certificates:

```
certificateKeyFile="conf/key.pem"
certificateFile="conf/cert.pem"
certificateChainFile="conf/mail.pem"
```

Go to the directory ** / home / alex / docker / drawio_build **. Checking Dockerfile:

> $ vi /home/alex/docker/drawio_build/Dockerfile


## Support Info

* Shell access whilst the container is running: `docker exec -it drawio /bin/bash`
* To monitor the logs of the container in realtime: `docker logs -f drawio`

## Via Docker Run/Create
* Update the image: `docker pull drpesojr/draw.io-ssl`
* Stop the running container: `docker stop drawio`
* Delete the container: `docker rm drawio`
* Start the new container: `docker start drawio`

