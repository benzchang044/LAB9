# LAB9
Car-Detection
import sys
from datetime import datetime
from cStringIO import StringIO
import cv2
import cv
import time
import math
import datetime
import numpy as np
from numpy import *
from cv import *
from cv2 import *

class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

#Hit Detectection Fuction
def hitchk(ax0, ay0, ax1, ay1, bx0, by0, bx1, by1):
    #topA = (float) np.min(ay0, ay1)
    if(ay0<ay1): 
	topA = ay0
    else:
 	topA = ay1   
    #botA = (float) np.max(ay0, ay1)
    if(ay0>ay1): 
	botA = ay0
    else:
	botA = ay1
    #leftA = (float) np.min(ax0, ax1)
    if(ax0<ax1): 
	leftA = ax0
    else:
 	leftA = ax1
    #rightA = (float) np.max(ax0, ax1)
    if(ax0>ax1): 
	rightA = ax0
    else:
	rightA = ax1
    #topB = (float) np.min(by0, by1)
    if(by0<by1): 
	topB = by0
    else:
 	topB = by1
    #botB = (float) np.max(by0, by1)
    if(by0>by1): 
	botB = by0
    else:
	botB = by1	
    #leftB = (float) np.min(bx0, bx1)
    if(bx0<bx1): 
	leftB = bx0
    else:
 	leftB = bx1
    #rightB = (float) np.max(bx0, bx1)
    if(bx0>bx1): 
	rightB = bx0
    else:
	rightB = bx1

    if((botA < topB)or(botB<topA)or(rightA<leftB)or(rightB<leftA)):
        return False
    return True  


backsub = cv2.BackgroundSubtractorMOG()
#capture2 = cv2.VideoCapture("video.avi")

#camera1
#capture = cv2.VideoCapture("t3.avi")
capture = cv2.VideoCapture("cam1.mp4")
#capture = cv2.VideoCapture("t3.avi")
#capture = cv2.VideoCapture('http://admin:123456@192.168.0.20/video1.mjpg')# IP Camera D-Link

#camera2
#capture2 = cv2.VideoCapture("cam2.mp4")
#capture2 = cv2.VideoCapture("cap2.avi")
#stream = urllib.urlopen('http://192.168.0.10/asp/video.cgi?profile=4&resolution=640x360&random=0.8661962583259903') # IP Camera TP-Link


today = datetime.datetime.today()
BlobHitCount=0
best_id=0


hit1=False
hit2=False
hit3=False
hit1_count=0
hit2_count=0
hit3_count=0
countcar=0
hitcc=0
#i = 0
num=0
if capture:
    
  while True:

    ret, frame = capture.read()
   
		
  
    if ret:
        #linesRed = cv2.line(frame,(110,223),(249,223),(0,0,255),3)
        #print("ok")
        fgmask = backsub.apply(frame, None, 0.01)
        contours, hierarchy = cv2.findContours(fgmask.copy(), cv2.RETR_EXTERNAL,cv2.CHAIN_APPROX_NONE)
        try: hierarchy = hierarchy[0]
        except: hierarchy = []	
	 #reset blobid each frame
	best_id=0
	#Define Traffic Line: Case of cap1.mp4
        #Wrong U-Turn Case
	#Hit 1
	cv2.line(frame,(400,300),(650,300),(0,255,0),2)
	#Cenvert Hit 1 Line to Rectangle Position
	hit1_x1=400
	hit1_y1=300
	hit1_x2=650
	hit1_y2=300
	

	
        for contour, hier in zip(contours, hierarchy):
            (x,y,w,h) = cv2.boundingRect(contour)
            #blob wxh filter apply**********************************************
            if w > 35 and h > 35:   
		#blobid+=1
                # figure out id
                best_id+=1
		area = w*h
		area1 = area
		#if area <= 1500 :
		if area <= 1500 :
			
			area = "motor"
			
		else :
			
			area = "car"
			
			num+=1

               
                #cv2.rectangle(frame, (x,y), (x+w,y+h), (0, 255,0), 2)
		
		cv2.putText(frame, str(area), (x,y-5), cv2.FONT_HERSHEY_SIMPLEX,
                        0.4, (255, 0, 0),1)
		
		
               
		
                

		#BlobCenter
		BlobCenterXY = Point(int(w/2)+int(x), int(h/2)+int(y))
		blobcenx = w
		blobceny = h
		blobcenter = int((x*y))

		#Draw Blob Center
	        cv2.circle(frame,(int(w/2)+int(x), int(h/2)+int(y)),1,(0,0,255),10);
		
		
		
		#Hit Consideration Here.....	
		#hit1=hitchk((float)(hit1_x1), (float)(hit1_y1), (float)(hit1_x2), (float)(hit1_y2), (float)(x), (float)(y), (float)(x+w), (float)(y+h))
		#blobcen =hitchk((float)(hit1_x1), (float)(hit1_y1), (float)(hit1_x2), (float)(hit1_y2), (float)((w/2)+x), (float)((h/2)+y), (float)(x+w), (float)(y+h))
		#blobcen =hitchk((float)(hit1_x1), (float)(hit1_y1), (float)(hit1_x2), (float)(hit1_y2), (float)(x+w), (float)(y+h), (float)((w/2)+x), (float)((h/2)+y))
		#blobcen =hitchk((float)(hit1_x1), (float)(hit1_y1), (float)(hit1_x2), (float)(hit1_y2), (float)(x+w), (float)(y+h), (float)(w-(w-1)), (float)((h/2)+y))
		blobcen =hitchk((float)(hit1_x1), (float)(hit1_y1), (float)(hit1_x2), (float)(hit1_y2), (float)(x+w), (float)(y+h), (float)((w/2)+x), (float)((h/2)+y)  )
		
		#Count Hit
		#if(hit1==True):
			#hit1_count+=1
		if((blobcen==True)):
			countcar+=(0.5)
			
			
			
		#Show Result
		#print "BLOB_CENTER_XY:",[int(w/2)+int(x), int(h/2)+int(y)]
		
		
			 
		#if((hit2_count>1)and(hit3_count>1)):
		#if(hit1_count<1):
			
			#countcar+=1
		print blobcen,x,y,w,h,today,area
	
	cv2.putText(frame, str("CAR:"),(50,50), cv2.FONT_HERSHEY_SIMPLEX,
                        0.8, (0, 255, 0),2)
	cv2.putText(frame, str(int(countcar)),(100,100), cv2.FONT_HERSHEY_SIMPLEX,
                        2, (0, 255, 0),3)
        cv2.imshow("Camera1(process)", frame)
        #cv2.imshow("bgsub", fgmask)
        
    key = cv2.waitKey(100)
    if key == ord('q'):
            break
    ###############################################################################################
    
    โจทย์พิเศษ
    
    create database car;
use car;
CREATE TABLE countcar13(id INT AUTO_INCREMENT,
	path VARCHAR(30),
	time TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
	PRIMARY KEY(id));

select * from countcar;


create database LAB6;
use LAB6;
CREATE TABLE users(id INT AUTO_INCREMENT,
	name VARCHAR(30),
	datum TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
	PRIMARY KEY(id));
INSERT INTO users(name) VALUES('Sivuch');
select * from users;


sudo apt-get install python-opencv
sudo apt-get install python-mysqldb


cv2.imwrite('/home/teekung/Desktop/Project/pic/''WL3-C1-ID'+str(best_id)+'-'+str(area)+'-'+str(today)+'.jpg', frame)
