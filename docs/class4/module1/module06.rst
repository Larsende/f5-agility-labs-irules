Lab 6 - File IO
================

#. Start an NGINX docker instance with the file_io app by running the following commands:  This places the file_io.conf file and file_io.js files into the running NGINX instance.

   .. code-block:: shell

      EXAMPLE=file_io
      docker run --rm --name njs_example  -v $(pwd)/conf/$EXAMPLE.conf:/etc/nginx/nginx.conf:ro  -v $(pwd)/njs/$EXAMPLE.js:/etc/nginx/example.js:ro -p 80:80 -p 8090:8090 -d nginx

   The nginx.conf will be as follows, for the /version it returns a version from the njs, /push will add info, /flush will remove info, /read will output the info.

   .. code-block:: nginx

      ...
  
      http {
         js_include example.js;
  
         server {
              listen 80;
  
              location /version {
                  js_content version;
              }
  
              location /push {
                  js_content push;
              }
  
              location /flush {
                  js_content flush;
              }
  
              location /read {
                  js_content read;
              }
         }
       }

   The njs code has 3 functions, push to store data in a file, flush to empty the data in file, read to output the data in file.

   .. code-block:: js

       var fs = require('fs');
       var STORAGE = "/tmp/njs_storage"

       function push(r) {
          fs.appendFileSync(STORAGE, r.requestBody);
          r.return(200);
       }

       function flush(r) {
          fs.writeFileSync(STORAGE, "");
          r.return(200);
       }

       function read(r) {
          var data = "";
          try {
              data = fs.readFileSync(STORAGE);
          } catch (e) {
          }

          r.return(200, data);
       }

#. To show this run the following commands:

   .. code-block:: shell

      curl http://localhost/read
      200 <empty reply>

      curl http://localhost/push -X POST --data 'AAA'
      200

      curl http://localhost/push -X POST --data 'BBB'
      200

      curl http://localhost/push -X POST --data 'CCC'
      200

      curl http://localhost/read
      200 AAABBBCCC

      curl http://localhost/flush -X POST
      200

      curl http://localhost/read
      200 <empty reply>

      docker stop njs_example
