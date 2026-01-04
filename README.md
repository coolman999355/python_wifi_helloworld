# micropython_wifi_helloworld
# today we are going to print hello in a website with micropython
# code u need to have a pi pico w or esp32 anything with wifi built in

import network   # for connecting to wifi
import socket    # for making a simple web server
import time      # for sleep function


port = 80

wlan = network.WLAN(network.STA_IF)
wlan.active(True)

ssid = "your wifi name exactly like it is"
password = "your wifi password"

wlan.connect(ssid, password)
while not wlan.isconnected():
    print("connecting...")
    time.sleep(1)

ip = wlan.ifconfig()[0]
print("connected ip address =", ip)

addr = socket.getaddrinfo(ip, port)[0][-1]
s = socket.socket()
s.bind(addr)
s.listen(1)
print("web is running")

html = """<!DOCTYPE html>
<html>
<head>
    <title>My MicroPython Page</title>
</head>
<body>
    <h1>Hello World! </h1>
    <p>This is my first MicroPython website.</p>
</body>
</html>
"""

while True:
    cl, addr = s.accept()
    cl.recv(1024)  # we ignore the request
    cl.send("HTTP/1.0 200 OK\r\nContent-Type: text/html\r\n\r\n")
    cl.send(html)
    cl.close()
