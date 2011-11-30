 Ruby Web Proxy Server

This simple Ruby web proxy uses the native Ruby TCPSocket class that is part of
Ruby's std-lib. The proxy caches web requests by default. It caches information
from the website you access as long as the cache for that for that request does
not exceed 1MB. The maximum size of the cache was arbitrarily chosen to be 50MB
and if the next subsequent web request would cause the cache to exceed the
maximum cache size of 50MB, we would search through the cache and look for the
sites that have been in the cache for an hour or more. Such information would
be deleted from the cache. The rationale for this is that we assume the user
would be browsing websites for a short period of time and a one hour threshold
is a reasonable threshold if the cache would be full.

When forwarding headers, I made sure that the "Host: <hostname>" request header
is sent to the server. This avoids any complications if the server uses virtual
hosting. Also, all requests were forwarded as version HTTP/1.0 even if the
original request was HTTP/1.1.Since HTTP/1.1 supports persistent connections by
default, the server won’t close the connection after it responds to an HTTP/1.1
request. If the request was forwarded as HTTP/1.0, we are asking the server to
close the connection after it sends the response. Thus, we can reliably use EOF
on the server connection to determine the end of the response. Also, I replaced
all "Connection/Proxy-Connection: [connection-token]" request headers with
"Connection/Proxy-Connection: close". All "Keep-Alive: [timeout-interval]"
request headers were removed as well. The reason for this is that some
misbehaving servers will sometimes use persistent connections even for HTTP/1.0
requests. You can force the server to close the connection after it has sent the
response by sending the Connection: close header. Simple string parsing was done
to modify the request headers.

This web proxy is a simple implementation that only services 'GET' requests
and ignores persistent connections. It is also single threaded and thus
performance may be slow. Other features were not implemented due to a lack of
time and may be included in the future.

Note: I recommend Firefox when using this web proxy.

# Configuring your browser before using this proxy

* Open Firefox.
* Go to Edit > Preferences > Advanced > Settings.
* Select "Manual proxy configuration:".
* Under the "HTTP Proxy:" field, enter "localhost".
* Under the "Port:" field, enter a port to listen on e.g. 8989.

# How do I use it?
	
If you were listening to port 8989 in your browser (configured as above),

	unix> ruby proxy.rb 8989

The format of websites you can visit include the following:

	http://www.somesite.com e.g. http://www.cs.cmu.edu/
	http://www.somesite.com/<filename> e.g. http://www.somesite.com/index.html
	http://www.somesite.com:<port number> e.g. http://www.somesite.com:8080
	http://www.somesite.com:<port number>/<filename> e.g. http://www.somesite.com:8080/index.html

Browse away!

# About

Created as part of a programming challenge from the people at SpeakerText for
consideration in their internship program.
