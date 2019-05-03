# sendingip
树莓派给我的126邮箱
#sendingip
#coding:utf-8
import socket,struct,fcntl
import smtplib
import urllib.request
import time
from email.mime.text import MIMEText
from email.header import Header

def http_validate(target_url):
    try:
        urllib.request.urlopen(target_url)
        return True
    except:
        return False

def get_ip(ifname):
    s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    return socket.inet_ntoa(fcntl.ioctl(s.fileno(), 0x8915, struct.pack('256s', bytes(ifname[:15],'utf-8')))[20:24])

url1 = 'https://www.baidu.com'
times = 0 
while times < 100:
    if http_validate(url1):
        break
    else:
        times=times+1
        time.sleep(3)
        
# 第三方 SMTP 服务
mail_host="smtp.126.com"  #设置服务器
mail_user="berryip@126.com"    #用户名
mail_pass="raspberry123"   #口令 
 
 
sender = 'berryip@126.com'
receivers = ['berryip@126.com']  # 接收邮件

newadd = get_ip('wlan0')
message = MIMEText("The ip_address is " + newadd, 'plain', 'utf-8')
message['From'] = Header("raspberrypi", 'utf-8')
message['To'] =  Header("receiver", 'utf-8')
 
subject = 'The ip_address has arrived!'
message['Subject'] = Header(subject, 'utf-8')


try:
    smtpObj = smtplib.SMTP() 
    smtpObj.connect(mail_host, 25)    # 25 为 SMTP 端口号
    smtpObj.login(mail_user,mail_pass)  
    smtpObj.sendmail(sender, receivers, message.as_string())
    print ("邮件发送成功")
except smtplib.SMTPException:
    print ("Error: 无法发送邮件")        
