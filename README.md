# image_interpolation
Interpolation between images using ffmpeg


# Convert raw NEFs to jpg

```python
import glob
import rawpy
import imageio
import time


# Timer is optional
print("Starting Now")
start = time.time()

# Sometimes the file ending can be .nef or .NEF, therefore I included both possibilities to save extra work.

pathNEF = "trios/*.NEF"
count = 0


for path in glob.glob(pathNEF):
    count = +1

    with rawpy.imread(path) as raw:
        rgb = raw.postprocess()
        imageio.imwrite(path.replace('.NEF', '') + '.jpg', rgb)
        count = count + 1
        print('image #', count, ' done')

# Timer is optional
end = time.time()
minutes = (end - start) / 60
seconds = (end - start) % 60

print("Elapsed Time:", round(minutes), "minutes and ", round(seconds), "seconds")

```

# MP4 creation

```python
import cv2
import numpy as np
import imageio
FPS = 30
def morph_transition(img1, img2, duration):
    rate = 1.0 / duration
    frames = []
    for i in range(duration):
        alpha = i * rate
        img_morphed = cv2.addWeighted(img1, 1 - alpha, img2, alpha, 0)
        frames.append(img_morphed)
    return frames

def create_video(images, display_duration, transition_duration):
    frames = []
    for i in range(len(images) - 1):
        img1 = cv2.imread("trios/"+images[i])
        img2 = cv2.imread("trios/"+images[i + 1])
        frames.extend([img1] * display_duration)
        frames.extend(morph_transition(img1, img2, transition_duration))
    frames.extend([cv2.imread("trios/"+images[-1])] * display_duration)
    
    imageio.mimsave('2_7.mp4', [cv2.cvtColor(frame, cv2.COLOR_BGR2RGB) for frame in frames], fps=FPS)

images = ["_CLI0152.jpg","_CLI0153.jpg","_CLI0154.jpg","_CLI0155.jpg","_CLI0156.jpg","_CLI0157.jpg","_CLI0158.jpg","_CLI0159.jpg","_CLI0160.jpg","_CLI0161.jpg","_CLI0162.jpg","_CLI0163.jpg","_CLI0164.jpg","_CLI0165.jpg"]
create_video(images, display_duration=2, transition_duration=7)

```
