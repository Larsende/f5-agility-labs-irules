Lab 4 - Subrequests join
========================

Combining the results of several subrequests asynchronously into a single JSON reply.

#. Start an NGINX docker instance with the join_subrequests app by running the following commands:  This places the join_subrequests.conf file and join_subrequests.js files into the running NGINX instance.

   .. code-block:: shell

      EXAMPLE=join_subrequests
      docker run --rm --name njs_example  -v $(pwd)/conf/$EXAMPLE.conf:/etc/nginx/nginx.conf:ro  -v $(pwd)/njs/$EXAMPLE.js:/etc/nginx/example.js:ro -p 80:80 -p 8090:8090 -d nginx

   The nginx.conf will be as follows, notice for the /join url it calls the njs with a join command: 

   .. code-block:: nginx

      ...

      http {
         js_include example.js;

         server {
            listen 80;

            location /join {
                js_content join;
            }

            location /foo {
                proxy_pass http://localhost:8080;
            }

            location /bar {
                proxy_pass http://localhost:8090;
            }
         }
      }

    The njs code joins the responses of the two other requests into a single output:

     .. code-block:: js

        function join(r) {
            join_subrequests(r, ['/foo', '/bar']);
        }

        function join_subrequests(r, subs) {
            var parts = [];

        function done(reply) {
             parts.push({ uri:  reply.uri,
                       code: reply.status,
                       body: reply.responseBody });

          if (parts.length == subs.length) {
              r.return(200, JSON.stringify(parts));
          }
        }

         for (var i in subs) {
            r.subrequest(subs[i], done);
            }
         }

#. To show this run the following commands:

    .. code-block:: shell

        curl http://localhost/join
        [{"uri":"/foo","code":200,"body":"FOO"},{"uri":"/bar","code":200,"body":"BAR"}]

        docker stop njs_example

