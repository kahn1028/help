# help

import os

import sys

import cv2
from PIL import Image
from PyQt5.QtGui import QPixmap, QImage
from PyQt5.QtWidgets import QApplication, QWidget, QPushButton, QLabel, QVBoxLayout, QHBoxLayout, QFileDialog, \
QLineEdit, QRadioButton, QMessageBox

class MainWindow(QWidget):
def __init__(self):
super().__init__()

self.top = 200
self.left = 500
self.width = 450
self.height = 600
self.imgwidth = 300
self.imgheight = 400
self.setWindowTitle("사진 속 얼굴 태깅 app")
self.setGeometry(self.left, self.top, self.width, self.height)
def loadImage(self):
self.pixmap = QPixmap(self.imagepath)
pixmap_resized = self.pixmap.scaled(self.imgwidth, self.imgheight)
self.label.setPixmap(pixmap_resized)
def getPhotoPath(self):
fname = QFileDialog.getOpenFileName(self, "Open file")
self.imagepath = fname[0]
self.originalpath = fname[0]
self.loadImage()
def createEditingWindow(self):
self.editwin = EditWindow()
self.editwin.setWidgets(self)
self.editwin.show()

def findFace(self):
self.flist = FaceList()
face_cascade = cv2.CascadeClassifier('haarcascade_frontalface_default.xml')
img = cv2.CasscadeClassifier(self.imagepath, cv2.IMREAD_COLOR)
img = cv2.resize(img, (self.imgwidth, self.imgheight))
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
faces = face_cascade.detectMultiScale(gray, 1.2, 1).tolist()
for (x, y, w, h) in faces:
print(x, y, w, h)
self.fList.append_face(x, y, w, h)
cv2.rectangle(img, (x, y), (x + w, y + h), (255, 0, 0), 2)
self.showImage(img)

def showImage(self, img):
height, width, colors = img.shape
bytesPerLine = 3 * width
image = QImage(img.data, width, height, bytesPerLine, QImage.Format_RGB888)
self.image = image.rgbSwapped()
self.label.setPixmap(QPixmap.fromImage(self.image))

def setWidgets(self):
self.label = QLabel("photo gets up loaded here.", self)
#self.label.move(20, 20)
self.btn1 = QPushButton("upload", self)
self.btn1.clicked.connect(self.getPhotoPath)
self.btn2 = QPushButton("edit photo, self)
self.btn2.clicked.connect(self.createEditingWindow)
self.btn3 = QPushButton("track face(makes crash)", self)
self.btn3.clicked.connect(self.findFace)
self.btn4 = QPushButton("button4", self)
self.btn5 = QPushButton("button5", self)
self.btn6 = QPushButton("button6", self)
#self.btn1.move(310, 20)
#self.btn2.move(310, 60)
#self.btn3.move(310, 100)
#self.btn4.move(310, 140)
#self.btn5.move(310, 180)
#self.btn6.move(310, 220)
hbox = QHBoxLayout()
hbox.addWidget(self.btn1)
hbox.addWidget(self.btn2)
hbox.addWidget(self.btn3)
hbox.addWidget(self.btn4)
hbox.addWidget(self.btn5)
hbox.addWidget(self.btn6)
self.setLayout(hbox)

buttons_widget = QWidget()
buttons_widget.setLayout(hbox)

vbox = QVBoxLayout()
vbox.addWidget(self.label)
vbox.addWidget(buttons_widget)
self.setLayout(vbox)

class EditWindow(MainWindow):
def __init__(self):
super().__init__()
self.width = 200
self.height = 300
self.setGeometry(self.left, self.top, self.width, self.height)
def setWidgets(self, mainwindow):
self.labelwidth = QLabel("width change)
self.textwidth = QLineEdit("width", self)
self.labelheight = QLabel("height")
self.textheight = QLineEdit("height",self)
self.labelcolor = QLabel("change color")

self.radiobtn1 = QRadioButton("normal")
self.radiobtn1.setChecked(True)
self.radiochecked = "normal"
self.radiobtn2 = QRadioButton("gray")
self.radiobtn3 = QRadioButton("red")
self.radiobtn4 = QRadioButton("green")
self.radiobtn5 = QRadioButton("blue")

self.radiobtn1.toggled.connect(self.btnstate)
self.radiobtn2.toggled.connect(self.btnstate)
self.radiobtn3.toggled.connect(self.btnstate)
self.radiobtn4.toggled.connect(self.btnstate)
self.radiobtn5.toggled.connect(self.btnstate)

self.btnOK = QPushButton('confirm', self)
self.btnOK.clicked.connect(lambda: self.editImage(mainwindow))

vbox = QVBoxLayout()
vbox.addWidget(self.labelwidth)
vbox.addWidget(self.textwidth)
vbox.addWidget(self.labelheight)
vbox.addWidget(self.textheight)
vbox.addWidget(self.labelcolor)
vbox.addWidget(self.radiobtn1)
vbox.addWidget(self.radiobtn2)
vbox.addWidget(self.radiobtn3)
vbox.addWidget(self.radiobtn4)
vbox.addWidget(self.radiobtn5)
vbox.addWidget(self.btnOK)
self.setLayout(vbox)
def editImage(self, mainwindow):
img = Image.open(mainwindow.originalpath)
if self.radiochecked == "normal":
img_edited = img
if self.radiochecked == "gray":
img_edited = img.convert("L")
if self.radiochecked == "red":
red = (
0.90, 0.36, 0.18, 0,
0.11, 0.72, 0.07, 0,
0.02, 0.12, 0.95, 0)
img_edited = img.convert("RGB", red)
if self.radiochecked == "green":
green = (
0.41, 0.36, 0.18, 0,
0.50, 0.72, 0.07, 0,
0.02, 0.12, 0.95, 0)
img_edited = img.convert("RGB", green)
if self.radiochecked == "blue":
blue = (
0.31, 0.36, 0.18, 0,
0.40, 0.72, 0.07, 0,
0.60, 0.12, 0.95, 0)
img_edited = img.convert("RGB", blue)

img_edited.save("image_edited.jpg", "JPEG")
mainwindow.imagepath = os.getcwd() + "\image_edited.jpg"
imgwidth_edited = self.textwidth.text()
imgheight_edited = self.textheight.text()
if imgwidth_edited == "width":
imgwidth_edited = mainwindow.imgwidth
if imgheight_edited == "height":
imgheight_edited = mainwindow.imgheight
try:
mainwindow.imgwidth = int(imgwidth_edited)
mainwindow.imgheight = int(imgheight_edited)
mainwindow.loadImage()
self.close()
except ValueError:
QMessageBox.question(self, "width or height input problem", "width or height's input is not number", QMessageBox.Ok)

def btnstate(self):
radiobtn = self.sender()
self.radiochecked = radiobtn.text()

class Face:
def __init__(self, x, y, w, h, name, idx):
self.x = x
self.y = y
self.w = w
self.h = h
self.name = name
self.id = idx

class FaceList:
def __init__(self):
self.face_list = []
self.next_id=0
def append_face(self, x, y, w, h):
self.face_list.append(Face(x, y, w, h, '', self.next_id))
self.next_id += 1
if __name__ == '__main__':
app = QApplication(sys.argv)
win = MainWindow()
win.setWidgets()
win.show()
app.exec_()

something is wrong
