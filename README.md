# JaiRadhe
With the increase in the amount of data produced, the storage space is always expanding; and owning and  managing this immense data is a tedious task. With this came the importance of cloud systems.
 With the cloud came the need for security. With more and more devices connecting on the internet, the risk of data being hampered increases.
Not just storing data, but storing data securely is the need of the hour.
 
My project aims to show how this need can be fulfilled. The cloud architecture is represented here using socket programming, where the two terminals act as client and server respectively.
When a client wants to store data in the cloud, it will upload the file to the cloud, where on receiving the file the cloud provider uses the public key to store the file in the encrypted format.

#decrypt_file.py

import hashlib
from Crypto import Random
from Crypto.Cipher import AES
from base64 import b64encode, b64decode
import random
import string
import math

########################################### AES CLASS ###############################################
class AESCipher(object):
    def __init__(self, key):
        self.block_size = AES.block_size
        self.key = hashlib.sha256(key.encode()).digest()

    def encrypt(self, plain_text):
        plain_text = self.__pad(plain_text)
        iv = Random.new().read(self.block_size)
        cipher = AES.new(self.key, AES.MODE_CBC, iv)
        encrypted_text = cipher.encrypt(plain_text.encode())
        return b64encode(iv + encrypted_text).decode("utf-8")

    def decrypt(self, encrypted_text):
        encrypted_text = b64decode(encrypted_text)
        iv = encrypted_text[:self.block_size]
        cipher = AES.new(self.key, AES.MODE_CBC, iv)
        plain_text = cipher.decrypt(encrypted_text[self.block_size:]).decode("utf-8")
        return self.__unpad(plain_text)

    def __pad(self, plain_text):
        number_of_bytes_to_pad = self.block_size - len(plain_text) % self.block_size
        ascii_string = chr(number_of_bytes_to_pad)
        padding_str = number_of_bytes_to_pad * ascii_string
        padded_plain_text = plain_text + padding_str
        return padded_plain_text

    @staticmethod
    def __unpad(plain_text):
        last_character = plain_text[len(plain_text) - 1:]
        return plain_text[:-ord(last_character)]
        
####################################################################################################################


################################################ RSA FUNCTIONS ##################################################


def rsa_decrypt(priv_key,c_text):
    d,n=priv_key
    txt=c_text.split(',')
    x=''
    m=0
    for i in range(0,len(txt)): 
        if(txt[i]=='400'):
            x+=' '
        elif(txt[i]=='401'):
            x+='\n'
        else:
            m=(int(txt[i])**d)%n
            m+=33
            c=chr(m)
            x+=c
      
    return x  

#################################################################################################################

filename=input("Enter your filename with extension: ")
d=int(input("Enter value of D: "))
n=int(input("Enter value of N: "))

private=d,n

key=input("Enter your AES key: ")

f=open(filename,'rb')
b=f.read()
f.close()

iv = "$%^GP(8l0sN-K06#"

aes = AES.new(key, AES.MODE_CBC, iv)
decd = aes.decrypt(b)

for_rsa=decd.decode()

rsa_dec=rsa_decrypt(private,for_rsa)
print("Decrypted your file sucessfully")

f=open(filename, 'w')
f.write(rsa_dec)
f.close()        

print("Thank You for Using Decryptor")
