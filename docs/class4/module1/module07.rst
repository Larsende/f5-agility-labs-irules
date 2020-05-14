Lab 7 - Complex redirects using njs file map.
=============================================

#. Start an NGINX docker instance with the complex_redirects app by running the following commands:  This places the complex_redirects.conf file and complex_redirects.js files into the running NGINX instance.


   .. code-block:: shell

     EXAMPLE=complex_redirects
     docker run --rm --name njs_example  -v $(pwd)/conf/$EXAMPLE.conf:/etc/nginx/nginx.conf:ro  -v $(pwd)/njs/$EXAMPLE.js:/etc/nginx/example.js:ro -p 80:80 -p 8090:8090 -d nginx

   The nginx.conf will be as follows, when going to the / path it will run the resolv code script.:

   .. code-block:: nginx

      ...

      http {
         js_include example.js;

         upstream backend {
           server 127.0.0.1:8080;
         }

         server {
            listen 80;

            location = /version {
                js_content version;
            }

            # PROXY

            location / {
                auth_request /resolv;
                auth_request_set $route $sent_http_route;

                proxy_pass http://backend$route$is_args$args;
            }

            location = /resolv {
                internal;

                js_content resolv;
            }
         }
         }


   The njs code is as follows:

   .. code-block:: js

       ...

       function resolv(r) {
           try {
               var map = open_db();
               var uri = r.variables.request_uri.split("?")[0];
               var mapped_uri = map[uri];

               r.headersOut['Route'] = mapped_uri ? mapped_uri : uri;
               r.return(200);

           } catch (e) {
               r.return(500, "resolv: " + e);
           }
        }
       ...

#. To check the code is working run the following commands:

   .. code-block:: shell

     curl http://localhost/CCC?a=1
     200 /CCC?a=1

     curl http://localhost:8090/map
     200 {}

     curl http://localhost:8090/add -X POST --data '{"from": "/CCC", "to": "/AA"}'
     200

     curl http://localhost:8090/add -X POST --data '{"from": "/BBB", "to": "/DD"}'
     200

     curl http://localhost/CCC?a=1
     200 /AA?a=1

     curl http://localhost/BB?a=1
     200 /BB?a=1

     curl http://localhost:8090/map
     200 {"/CCC":"/AA","/BBB":"/DD"}

     curl http://localhost:8090/remove -X POST --data '{"from": "/CCC"}'
     200

     curl http://localhost:8090/map
     200 {"/BBB":"/DD"}

     curl http://localhost/CCC?a=1
     200 /CCC?a=1

     docker stop njs_example

