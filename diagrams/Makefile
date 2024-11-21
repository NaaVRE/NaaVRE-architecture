drawio=$(wildcard *.drawio)
drawio_png=$(drawio:.drawio=.png)

all : $(drawio_png)

%.png : %.drawio
	drawio $< -xo $@
