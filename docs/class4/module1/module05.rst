Lab 5 - Secure hash
===================

Protecting ``/secure/`` location from simple bots and web crawlers.

#. Start an NGINX docker instance with the secure_link_hash app by running the following commands:  This places the secure_link_hash.conf file and secure_link_hash.js files into the running NGINX instance.

   .. code-block:: shell

      EXAMPLE=secure_link_hash
      docker run --rm --name njs_example  -v $(pwd)/conf/$EXAMPLE.conf:/etc/nginx/nginx.conf:ro  -v $(pwd)/njs/$EXAMPLE.js:/etc/nginx/example.js:ro -p 80:80 -p 8090:8090 -d nginx

    The nginx.conf will be as follows, notice that when going to the /secure/ URI you will redirect to a login error page unless the cookie exists.     

   .. code-block:: nginx

      ...

      http {
         js_include example.js;

         js_set $new_foo create_secure_link;

         server {
            listen 80;

            location /secure/ {
                error_page 403 = @login;

                secure_link $cookie_foo;
                secure_link_md5 "$uri mykey";

                if ($secure_link = "") {
                        return 403;
                }

                proxy_pass http://localhost:8080;
             }

             location @login {
                add_header Set-Cookie "foo=$new_foo; Max-Age=60";
                return 302 $request_uri;
             }
           }
       }

   The njs code checks the hash of the cookie to validate correctness. 

   .. code-block:: js

      function create_secure_link(r) {
      return require('crypto').createHash('md5')
                            .update(r.uri).update(" mykey")
                            .digest('base64url');
      }

#. To show this run the following commands:

   .. code-block:: shell

      curl http://127.0.0.1/secure/r
      302

      curl http://127.0.0.1/secure/r -L
      curl: (47) Maximum (50) redirects followed

      curl http://127.0.0.1/secure/r --cookie-jar cookie.txt
      302

      curl http://127.0.0.1/secure/r --cookie cookie.txt
      PASSED

      docker stop njs_example
