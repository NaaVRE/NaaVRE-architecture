drawio=$(wildcard *.drawio)
drawio_png=$(drawio:.drawio=.png)

all : $(drawio_png) architecture-components.png architecture-communication.png

%.png : %.drawio
	drawio $< -xo $@

architecture-components.png : architecture-components-communication.drawio
	drawio $< -xo $@ -l 0,2

architecture-communication.png : architecture-components-communication.drawio
	drawio $< -xo $@ -l 0,1
