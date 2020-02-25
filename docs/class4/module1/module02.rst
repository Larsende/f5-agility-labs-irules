Lab 2 - Decode URI
==================

#. Start an NGINX docker instance with the decode_uri app by running the following commands:  This places the decode_uri.conf file and decode_uri.js files into the running NGINX instance.

   .. code-block:: shell

      EXAMPLE=decode_uri
      docker run --rm --name njs_example  -v $(pwd)/conf/$EXAMPLE.conf:/etc/nginx/nginx.conf:ro  -v $(pwd)/njs/$EXAMPLE.js:/etc/nginx/example.js:ro -p 80:80 -p 8090:8090 -d nginx

   The nginx.conf will be as follows, notice it sends the /uri 'dec_foo' to a njs function:

   .. code-block:: nginx

       ...

       http {
         js_include example.js;

         js_set $dec_foo dec_foo;

         server {
      ...
      
            location /foo {
                return 200 $arg_foo;
            }

            location /dec_foo {
                return 200 $dec_foo;
            }
          }
      }

   The njs decode_uri.js file is as follows.  Notice it takes the arguments sent to it and decodes it to readable format:

   .. code-block:: js

      function dec_foo(r) {
        return decodeURIComponent(r.args.foo);
      }

#. To see it work run the following commands from the linux shell:

   .. code-block:: shell

      curl -G http://localhost/foo --data-urlencode "foo=Hello World"
      Hello%20World

      curl -G http://localhost/dec_foo --data-urlencode "foo=Hello World"
      Hello World

      docker stop njs_example
