Reverse Proxy Example
---------------------
KPMP requires a reverse proxy server solution, allowing us to run multiple containerized applications behind a single IP address.

After some review of nginx-based options, I came across [https://github.com/jwilder/nginx-proxy](jwilder/nginx-rpoxy).  This is a highly-subscribed, stable nginx proxy container.  It supports HTTPS, which I've been able to verify locally with self-signed SSL certs.

What makes this option appealing:
* Excellent documentation
* Uses Docker API to observe when containers start and stop
* Driven by simple configuration at `docker run`-time (easy to configure in docker-compose.yml files)
* Supports HTTPS with very minimal effort; good defaults that can be overridden, like forced HTTPS-forwarding

All you have to do is start your container images with the environment variable `VIRTUAL_HOST` (as in `VIRTUAL_HOST=upload.kpmp.org`).  The nginx-proxy container detects when new containers with this variable start and stop, and automatically adds or removes them from the nginx proxy configurations appropriately.

Testing With HTTP
-----------------
1. Make sure your hosts file points `thing1.test` and `thing2.test` to localhost.
2. Ensure your docker.sock volume mapping in `docker-compose.yml` is correct (On OSX, it is `var/run/docker.sock`)
2. `docker-compose up`
3. Open `http://thing1.test`; you'll see a hash for the container
4. Open `http://thing2.test`; you'll see a different hash for the second container

Testing With HTTPS
------------------
1. Make sure your hosts file points `thing1.test` and `thing2.test` to localhost.
2. Ensure your docker.sock volume mapping is right.
3. Have self-signed certs stored in `~/certs` for both sites.
4. `docker-compose -f https-docker-compose.yml up`
4. Open `https://thing1.test` and ignore/accept security warning; you'll see a hash for the container
5. Open `https://thing2.test` and ignore/accept security warning; you'll see a different hash for the second container
