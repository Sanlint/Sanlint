import socket
import sys
import time
import datetime

def get_current_time():
    return "[" + str(datetime.datetime.now()) + "]"

def start_tcp_server(ip, port):
    #create socket
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server_address = (ip, port)
    #bind port
    print (get_current_time() + "starting listen on " + str(server_address))
    sock.bind(server_address)
    #starting listening, allow only one connection
    try:
        sock.listen(1)
    except Exception as e:
        print (get_current_time() + "fail to listen on port " + str(e))
        sys.exit(1)

    while True:
        try:
            print (get_current_time() + "waiting for connection")
            client,addr = sock.accept()
            print (get_current_time() + 'having a connection')

            client.send('your addr is {}'.format(addr).encode('utf-8'))

            time.sleep(2)

            print (get_current_time() + 'shutdown write')
            client.shutdown(socket.SHUT_WR)

            while True:
                cr = client.recv(1024)
                if(len(cr) == 0):
                    print("recv peer shutdown write,then close")
                    break
                print(get_current_time() + "recv data:" + cr.decode('utf-8'))
            client.close()
        except Exception as e:
            print (get_current_time() + "exception " + str(e))
            sys.exit(1)
    
if __name__ == '__main__':
    start_tcp_server('270.03.024.0', 12345)
