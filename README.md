# EXN-yolo
# Description
We propose an enhanced YOLOv5 model specifically tailored for micro-defect detection on KDP crystals.
# **Experimental** **setup**
This study's experimental configuration was established on a system operating under Windows 10, outfitted with an i7-8750H CPU and an NVIDIA GeForce RTX 1060 GPU. Experiments were accelerated using CUDA 11.8, based on PyTorch 2.1.0, with identical hyper-parameters for  training and validation. 
# Download
The dataset and models have been released. [Data&Model]( https://pan.baidu.com/s/1CNXxqazJ0FbLtPdP2yIL0A?pwd=s78b )
- The link includes our own KDP micro-defect dataset.
- This model includes the redesigned Backbone layer of the YOLOv5s model, the introduction of the XIoU loss function, and the construction of a novel combination of XIoU and NWD loss functions.
# Usage Instructions
Please visit the https://github.com/ultralytics/yolov5/tree/v6.1 website to download the YOLOv5 v6.1. After downloading YOLOv5 v6.1, replace the existing yolov5s.yaml file with the EXN-yolo.yaml file from the Data&Model folder. Next, navigate to the yolov5-6.1/yolov5-6.1/models/ directory and place the efficientv2.py file under the models subdirectory. These two steps successfully integrate the efficientnetv2 as the backbone structure. Finally, open the yolov5-6.1/yolov5-6.1/utils/loss.py file and add the content from the XIoU+NWD.py file located in the Data&Model folder to the loss.py file, thus successfully incorporating the new loss function XIoU+NWD.
### Dataset Directory Structure
~~~
VOCdevkit/
├── images
│   ├── train
│   └── val
└── labels
    ├── train
    └── val
### xml_txt
~~~
import os  
import glob  
import xml.etree.ElementTree as ET  
  
xml_file=r'' #xml_file  
  
l=['bottle']  
  
def convert(box,dw,dh):  
    x=(box[0]+box[2])/2.0  
  y=(box[1]+box[3])/2.0  
  w=box[2]-box[0]  
    h=box[3]-box[1]  
  
    x=x/dw  
    y=y/dh  
    w=w/dw  
    h=h/dh  
  
    return x,y,w,h  
  
def f(name_id):  
    xml_o=open(r''%name_id)  # original xml file path
    txt_o=open(r''%name_id,'w')  # the current txt file
  
    pares=ET.parse(xml_o)  
    root=pares.getroot()  
    objects=root.findall('object')  
    size=root.find('size')  
    dw=int(size.find('width').text)  
    dh=int(size.find('height').text)  
  
    for obj in objects :  
        c=l.index(obj.find('name').text)  
        bnd=obj.find('bndbox')  
  
        b=(float(bnd.find('xmin').text),float(bnd.find('ymin').text),  
  float(bnd.find('xmax').text),float(bnd.find('ymax').text))  
  
        x,y,w,h=convert(b,dw,dh)  
  
        write_t="{} {:.5f} {:.5f} {:.5f} {:.5f}\n".format(c,x,y,w,h)  
        txt_o.write(write_t)  
  
    xml_o.close()  
    txt_o.close()  
  
name=glob.glob(os.path.join(xml_file,"*.xml"))  
for i in name :  
    name_id=os.path.basename(i)[:-4]  
    f(name_id)
~~~
