init:
	docker run -it -v /tmp/.X11-unix:/tmp/.X11-unix \
		-v /dev:/dev \
		-v /media:/media \
  		-e DISPLAY=unix$DISPLAY \
  		-e GDK_SCALE \
  		-e GDK_DPI_SCALE \
                --name os \
		cyh834/our-os:v1.0

run:
	xhost +
	docker start -ai os
