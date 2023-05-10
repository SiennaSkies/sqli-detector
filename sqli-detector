import socket
import requests
import numpy as np

def build_request(req, allowredirect=True):

    req = req.decode('utf-8')
    url = req.strip().split('\r\n')[0].split()[1]
    headers = {}
    for i in req.strip().split('\r\n\r\n')[0].split('\r\n')[1:]:
        headers[i.split(':')[0].replace(' ','')] = i.split(':')[1].replace(' ','')
    method = req.strip().split('\r\n')[0].split()[0]
    if method == 'GET':
        repeat_get(req, url, headers)
    elif method == 'POST':
        data =  req.strip().split('\r\n\r\n')[1]
        repeat_post(req, url, headers, data)

def find_mark(String, subString):
    start = 0
    posNum = []
    while True:
        start = String.find(subString, start)
        if start == -1:
            return posNum
        posNum.append(start)
        start += len(subString)

def repeat_get(req, url, headers):
    if '=' in url:
        posNum = find_mark(url, '=')
        for pos in posNum:
            repeat_url = url
            maikList = ["'", '"']
            for mark in maikList:
                status_list = []
                response_length = []
                for i in range(3):
                    repeat_url = url
                    repeat_url = list(repeat_url)
                    repeat_url.insert(pos+1, mark * i)
                    repeat_url = ''.join(repeat_url)
                    response = requests.get(url=repeat_url, headers=headers)
                    status_list.append(response.status_code)
                    response_length.append(len(response.text))
                judgement(req, status_list, response_length)

def repeat_post(req, url, headers, data):
    if '=' in data:
        posNum = find_mark(data, '=')
        for pos in posNum:
            repeat_data = data
            maikList = ["'", '"']
            for mark in maikList:
                status_list = []
                response_length = []
                for i in range(3):
                    repeat_data = data
                    repeat_data = list(repeat_data)
                    repeat_data.insert(pos+1, mark * i)
                    repeat_data = ''.join(repeat_data)
                    response = requests.post(url=url, headers=headers, data=repeat_data)
                    status_list.append(response.status_code)
                    response_length.append(len(response.text))
                judgement(req, status_list, response_length)

def judgement(req, status_list, response_length):

    if status_list[0] ==200 and status_list[1] != 200 and status_list[2] == 200:
        with open("text.txt","a") as file:
            file.write(req.rep)
            print('SQL injection founded!')
    elif np.var([response_length[0], response_length[2]]) < np.var([response_length[0], response_length[1]]) and np.var([response_length[1], response_length[2]]):
        with open("text.txt","a") as file:
            file.write(req)
            print('SQL injection founded!')

def get_request():

    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.bind(('127.0.0.1', 3333))
        s.listen(1)
    except Exception as e:
        print(e)
    
    while True:
        try:
            conn, addr = s.accept()
            req = conn.recv(2048)
            get_response(conn, addr, req)
            build_request(req)
        except Exception as e:
            print(e)

def get_response(conn, addr, req):
    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.connect(('TargetIP', TargetPort))
        s.send(req)

        while True:
            reply = s.recv(8192)
            if len(reply) > 0:
                conn.send(reply)
            else:
                break
        s.close()
        conn.close()
    except Exception as e:
        print(e)
        s.close()
        conn.close()


if __name__ == "__main__":
    get_request()
