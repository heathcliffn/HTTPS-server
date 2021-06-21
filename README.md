from http.server import BaseHTTPRequestHandler, HTTPServer
from urllib.parse import urlparse
import json
import pymongo
from pymongo import MongoClient
import base64
username = 'Mendsaikhan'
password = 'Mendee@1234'
userpass = username + ':' + password
encoded_u = base64.b64encode(userpass.encode()).decode()
headers = {"Authorization" : "Basic %s" % encoded_u}
print("done")
client = pymongo.MongoClient('mongodb://127.0.0.1:27017/')
class RequestHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        content_len = int(self.headers.get('content-length'))
        post_body = self.rfile.read(content_len)
        data = json.loads(post_body)
        print(data)
        mydb=client['inc3']
        info=mydb.test
        record={"test":data}
        info.insert_one(record)
        print("saved")

        parsed_path = urlparse(self.path)
        self.send_response(200)
        self.end_headers()
        self.wfile.write(json.dumps({
            'method': self.command,
            'path': self.path,
            'real_path': parsed_path.query,
            'query': parsed_path.query,
            'request_version': self.request_version,
            'protocol_version': self.protocol_version,
            'body' : data
        }).encode())
       return

    def do_POST(self):
        content_len = int(self.headers.get('content-length'))
        post_body = self.rfile.read(content_len)
        data = json.loads(post_body)
        print(data)
        db=client['post']
        info=db.test
        record={"post":data}
        info.insert_one(record)
        print("saved")

        parsed_path = urlparse(self.path)
        self.send_response(200)
        self.end_headers()
        self.wfile.write(json.dumps({
            'method': self.command,
            'path': self.path,
            'real_path': parsed_path.query,
            'query': parsed_path.query,
            'request_version': self.request_version,
            'protocol_version': self.protocol_version,
            'body': data
        }).encode())
        return
           
if __name__ == '__main__':
    server = HTTPServer(('139.162.127.185', 8081), RequestHandler)
    print('Listening....')
    
    server.serve_forever()

                           
                           
