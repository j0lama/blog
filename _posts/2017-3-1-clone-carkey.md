---
layout: post
title: How to clone a car key
---

Recently, a friend told me that he had bought a USB antenna to see the TV on the computer (R820T2) and that this antenna can capture traffic in a big frequency range, not only TV range. This antenna can capture frequencies of garage and car keys although it is designed to TV frequencies.

```python
import os
import cv2
import numpy as np

def parseData():

    clasesDict = {'A':0, 'B':1, 'C':2, 'D':3, 'E':4, 'F':5, 'G':6, 'H':7, 'I':8, 'J':9}
    imagenes = []
    imagenesResize = []
    clases = []
    ancho = 0
    alto = 0
    
    files = os.listdir("./images/") # Listamos los nombres de los archivos que hay en la carpeta images/
    
    for filename in files:
        clase = clasesDict[filename[-5]] # Obtenemos la clase de la imagen
        img = cv2.imread('./images/' + filename,0) # Leemos la imagen
        kernel = np.ones((7, 7), np.uint8) # Kernel de una vecindad de 7x7 para hacer el cierre
        _, umbralizacion = cv2.threshold(img, 0, 255, cv2.THRESH_BINARY+cv2.THRESH_OTSU) # Umbralizamos la imagen usando OTSU (Minimiza la varianza intra clase) de manera binaria
        cierre = cv2.morphologyEx(umbralizacion, cv2.MORPH_CLOSE, kernel) # Aplicamos un cierre de 7x7 sobre negros
        norte = 0
        sur = -1
        este = -1
        oeste = 0
        while not (0 in cierre[norte]):
            norte += 1
        while not (0 in cierre[sur]):
            sur -= 1
        while not (0 in cierre[:,oeste]):
            oeste += 1
        while not (0 in cierre[:,este]):
            este -= 1
        recortada = cierre[norte:206+sur+1,oeste:150+este+1] #Imagen recortada
        ancho += recortada.shape[0]
        alto += recortada.shape[1]
        imagenes.append(recortada) # Anadimos la imagen a la lista de imagenes
        clases.append(clase) # Anadimos la clase a la lista de clases
    anchoMedia = ancho // len(imagenes)
    altoMedia = alto // len(imagenes)
    print(anchoMedia)
    print(altoMedia)
    print(len(imagenes))
    for imagen in imagenes:
        imagenesResize.append(cv2.resize(imagen, (altoMedia, anchoMedia)))
    return np.array(imagenesResize), np.array(clases)
```

With this info I decided to buy one of this TV antennas that are very cheap, only 10$ ([R820T2 TV antenna](https://es.aliexpress.com/item/FW1S-New-USB-2-0-Digital-DVB-T-SDR-DAB-FM-HDTV-TV-Tuner-Receiver-Stick/32600825233.html?spm=a2g0s.11045068.rcmd404.2.266956a4uWrjNg&pvid=54eedbd9-24c7-42a3-9eed-ab60d3f9f07c&gps-id=detail404&scm=1007.16891.96945.0&scm-url=1007.16891.96945.0&scm_id=1007.16891.96945.0))