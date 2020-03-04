Lab 3 - Injecting HTTP header using stream proxy
================================================

#. Start an NGINX docker instance with the inject_header app by running the following commands:  This places the inject_header.conf file and inject_header.js files into the running NGINX instance.

   .. code-block:: shell

      EXAMPLE=inject_header
      docker run --rm --name njs_example  -v $(pwd)/conf/$EXAMPLE.conf:/etc/nginx/nginx.conf:ro  -v $(pwd)/njs/$EXAMPLE.js:/etc/nginx/example.js:ro -p 80:80 -p 8090:8090 -d nginx

   The nginx.conf will be as follows, notice it includes the njs script and calls the inject_header function for every request:

      .. code-block:: nginx

         ...

         stream {
            js_include example.js;

         server {
            listen 80;

            proxy_pass 127.0.0.1:8080;
            js_filter inject_header;
            }
        }

         ...

     The njs code adds an HTTP header called Foo with a value of my_foo:

        .. code-block:: js

           function inject_header(s) {
               inject_my_header(s, 'Foo: my_foo');
           }

           function inject_my_header(s, header) {
                var req = '';

            s.on('upload', function(data, flags) {
               req += data;
               var n = req.search('\n');
               if (n != -1) {
                 var rest = req.substr(n + 1);
                 req = req.substr(0, n + 1);
                 s.send(req + header + '\r\n' + rest, flags);
                 s.off('upload');
               }
            });
           }

#. To validate that it is working run the following commands:

   .. code-block:: shell

     curl http://localhost/
     my_foo

     docker stop njs_example

