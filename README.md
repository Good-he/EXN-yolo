# EXN-yolo
# Description
We propose an enhanced YOLOv5 model specifically tailored for micro-defect detection on KDP crystals.
# **Experimental** **setup**
This study's experimental configuration was established on a system operating under Windows 10, outfitted with an i7-8750H CPU and an NVIDIA GeForce RTX 1060 GPU. Experiments were accelerated using CUDA 11.8, based on PyTorch 2.1.0, with identical hyper-parameters for  training and validation. 
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
