# SMTP server setup
```py
import smtplib
from email.mime.text import MIMEText
# from config import LOGIN, PASS

LOGIN = 'YOUR_LOGIN'
PASS = 'YOUR_PASSWORD'

def send_email(msg:str):
	server = smtplib.SMTP('smtp.gmail.com', 587)
	server.starttls()
	server.login(LOGIN, PASS)

	msg = MIMEText(msg)
	msg['Subject'] = 'CLICK ME PLEASE!'

	recipients = [LOGIN,]
	# with open('recipients', 'r') as f:
	# 	recipients = [i.replace('\n','') for i in f.readlines()]

	server.sendmail(LOGIN, recipients, msg.as_string())

	return 'The message was sent successfully!'


def main():
	msg = input('Type your message: ')
	print(send_email(msg))


if __name__ == '__main__':
	main()
```
