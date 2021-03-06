#Smart Surveillance System

This directory contains two files that bring together all the various topics we have discussed such as AWS S3, OpenCV, Twilio, etc to create a simple "Smart Surveillance" system.

`motion_surveillance.py` utilizes the motion detection algorithms to trigger the alert while `face_eye_surveillance.py` utilizes the face and eye detection algorithms to trigger the alert.

**PLEASE NOTE THAT YOU NEED TO RUN THROUGH ALL THE VARIOUS EXERCISES PRIOR TO THIS TO GET THIS TO WORK AS THERE ARE SEVERAL REQUIRED PACKAGES THAT ARE INSTALLED AS WELL AS SERVICE CREDENTIALS CREATED/USED IN THESE OTHER EXERCISES THAT NEED TO BE USED HERE AS WELL!**

##face_eye_surveillance.py
This code utilizes the face and eye detection algorithms to trigger an alert. In order increase the confidence in the detection algorithm, I added some code that made sure that we detected 1 face and 2 eyes for 10 successive frames as shown in the code snippet below.
```python
if len(eyes) == 2:
				state = state + 1
				# draw the text and timestamp on the frame
				cv2.putText(frame, "State Value: {}".format(state), (10, 20), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (0, 0, 255), 2)
				# Make sure that it is really an accurate detection by
				# checking if it meets the condition for at 10 frames
				if state > 10:
					img_file = "img-%s.jpg"%datetime.datetime.now().strftime("%Y%m%d%H%M%S")
					cv2.imwrite(img_file, frame)
					send(img_file, s3_bucket)
					# Set the state variable back to 0 so it does not
					# fire successive alerts
					state = 0
			else:
				state = 0
```
We can start the face/eye detection based smart surveillance program by calling the following command
```
python face_eye_surveillance.py haarcascade_frontalface_default.xml haarcascade_eye.xml
```
##motion_surveillance.py
This code utilizes the motion detection algorithms to trigger an alert. In order to capture more of the change in motion, I added code that captures two images as shown in the code snippet below.
```python
# Capture Image if Status changes from Unoccupied to Occupied
if (state - prev_state) > 0:
	print("Motion Detected")
	img_files = []
	img_file = "img-%s.jpg"%datetime.datetime.now().strftime("%Y%m%d%H%M%S")
	print(img_file)
	img_files.append(img_file)
	cv2.imwrite(img_file, frame)
	motion_timer_start = True

# Set timer to capture second image after MOTION_TIMER_DELAY frames
if motion_timer_start:
	if motion_timer == MOTION_TIMER_DELAY:
		img_file = "img-%s.jpg"%datetime.datetime.now().strftime("%Y%m%d%H%M%S")
		img_files.append(img_file)
		cv2.imwrite(img_file, frame)
		send(img_files, s3_bucket)
		motion_timer = 0
		motion_timer_start = False
	motion_timer = motion_timer + 1

# Store current state into previous state
prev_state = state
```
We can start the motion based smart surveillance program by calling the following command
```
sudo python motion_surveillance.py -a 5000
```
##Results
|Description|OpenCV Screenshot|MMS Alert Screenshot|
|------------|:-------------:|:-------------:|
|face_eye_surveillance|![alt text](https://github.com/mvartani76/iot-detroit-jan2017/blob/master/Images/face_eye_surveillance_result.jpg "Face/Eye Surveillance Screenshot")|![alt text](https://github.com/mvartani76/iot-detroit-jan2017/blob/master/Images/face_eye_surveillance_mms_alert_small.png "Face/Eye Surveillance MMS Alert")|
|motion_surveillance|![alt text](https://github.com/mvartani76/iot-detroit-jan2017/blob/master/Images/motion_surveillance_result1.jpg "Motion Surveillance Screenshot") ![alt text](https://github.com/mvartani76/iot-detroit-jan2017/blob/master/Images/motion_surveillance_result2.jpg "Motion Surveillance Screenshot")|![alt text](https://github.com/mvartani76/iot-detroit-jan2017/blob/master/Images/motion_surveillance_mms_alert_small.png "Motion Surveillance MMS Alert")|
