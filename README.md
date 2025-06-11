# Neopixel-based-on-bitstream
Neopixel based on bitstream in micropython

## MyNeopixel.py：
```
from machine import Pin, bitstream

class neopixel():#GRB
    def __init__(self,pin_id='G11',num=240,timing=[350, 800, 700, 600]):
        self.pin=Pin(pin_id, Pin.OUT)
        self.num=num
        self.strip=bytearray([0,0,0]*self.num)
        self.timing=timing

    def fill_rect(self,xy2num,x0=0,y0=0,x1=18,y1=18,color=[0,0,0]):#以矩形填充一片区域
        for x in range(x0,x1):#填充x0-x1
            for y in range(y0,y1):#填充y0-y1
                led_id=xy2num[x+y*18]*3
                self.strip[led_id:led_id+3]=color
        
    def fill(self,color=[0,0,0]):#填充同一种颜色
        self.strip=bytearray(color*self.num)

    def write(self):
        bitstream(self.pin, 0, self.timing, self.strip)
        
    def clear(self):#黑屏
        self.strip=bytearray([0,0,0]*self.num)
        self.write()
        
    def change(self,led_id=0,color=[0,0,0]):
        self.strip[led_id*3:(led_id+1)*3]=bytearray(color)

    def print_word(self,word_dict={'A':[[1, 0], [2, 0]]},word='A',color=[0,0,0],dx=0,dy=0,word_width=5,screen_x=18,screen_y=18):
        for w in word:#取出第一个字
            for p in word_dict[w]:#取出该字的点
                x=p[0]+dx
                y=p[1]+dy
                if 0<x<screen_x and 0<y<screen_y:#不能超出屏幕范围
                    led_id=xy2num[x+y*screen_x]*3
                    self.strip[led_id:led_id+3]=bytearray(color)
            dx+=word_width
```

## Usage:
```
import time
from base_color import *
from MyNeopixel import *
from xy2num import *


neo=neopixel()
neo.clear()
st=time.ticks_us()
for j in range(36):
    for i in range(240):
        neo.change(i,white)
    neo.write()
print((time.ticks_us()-st)/1000000)
neo.clear()

import gc
gc.collect()
from word3x5 import *

dx=0
try:
    while 1:
        neo.print_word(word_dict=word3x5,word='ABC123',color=[1,1,1],dx=dx,dy=5,word_width=4)
        neo.write()
        time.sleep(0.5)
        neo.print_word(word_dict=word3x5,word='ABC123',color=[0,0,0],dx=dx,dy=5,word_width=4)
        neo.write()
        dx-=1
except:
    neo.clear()

```
