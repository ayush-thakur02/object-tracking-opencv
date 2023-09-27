<div align="justify" markdown="1">

  
# Object Tracking and Detection using OpenCV in python


Before starting with Object Tracking and Object detection you must make sure that you have installed all the necessary libraries. If you don’t have Opencv installed this is the command to run

```bash
pip install opencv-python
```

<div align="center" markdown="1">

Make Sure to follow me:

[![Github](https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white)](https://github.com/ayush-thakur02)
[![BioLink](https://img.shields.io/badge/bio.link-000000%7D?style=for-the-badge&logo=biolink&logoColor=white)](https://bio.link/ayush_thakur02)

</div>

## Object detection

For convenience, I have already written this part and you find everything in the object_detection.py file. To use it just a call in the main file

```python
...
from object_detection import ObjectDetection
...

# Initialize Object Detection
od = ObjectDetection()

while True:
...
    # Detect objects on frame
    (class_ids, scores, boxes) = od.detect(frame)
    for box in boxes:
        (x, y, w, h) = box
        cx = int((x + x + w) / 2)
        cy = int((y + y + h) / 2)
        center_points_cur_frame.append((cx, cy))
...
```

Here is the result in the video frame.

<a href="https://ibb.co/5xcsHJW"><img src="https://i.ibb.co/7Nyrm0J/object-tracking-from-scratch-opencv-and-python-object-detection-with-yolo-768x648.png" alt="object-tracking-from-scratch-opencv-and-python-object-detection-with-yolo-768x648" border="0"></a>

## Object Tracking
By saving the position of the center point of each object, you can trace the previous position of the objects and predict what the immediate next will be. Here is a small example in the image

<a href="https://ibb.co/fMYj0MV"><img src="https://i.ibb.co/kKgNcKV/object-tracking-from-scratch-opencv-and-python-point-tracking.png" alt="object-tracking-from-scratch-opencv-and-python-point-tracking" border="0"></a>

Before going on with the explanation, I must point out that object tracking is more complicated than that. Consider this tutorial as a simple exercise to understand the basics behind this algorithm. 

### Find the point and assign the ID

We don’t need the history of all the tracking but only the last points so Initialize an array to keep track of the previous points and then we need to calculate the distance between the points to make sure they all belong to the same object. The closer the points are, the greater the probability that we are tracking the same object.

```python

...
# Initialize count
count = 0
center_points_prev_frame = []

tracking_objects = {}
track_id = 0
...

    # Only at the beginning we compare previous and current frame
    if count <= 2:
        for pt in center_points_cur_frame:
            for pt2 in center_points_prev_frame:
                distance = math.hypot(pt2[0] - pt[0], pt2[1] - pt[1])

                if distance < 20:
                    tracking_objects[track_id] = pt
                    track_id += 1
...
```

As you can see from the portion of code above with the math.hypot() function the distance of the two points is calculated and if the distance is less than 20, an ID is associated with the position of the point.

<a href="https://ibb.co/z5KpXD3"><img src="https://i.ibb.co/YWvmcw5/object-tracking-from-scratch-opencv-and-python-assign-ID-1024x795.png" alt="object-tracking-from-scratch-opencv-and-python-assign-ID-1024x795" border="0"></a>

### Assign univocal ID

In this part of the code, we have to make sure to compare the previous object with the current one and update the position of the ID. In this way, the same object remains with the same ID for its entire path. When the object is no longer recognized, it loses the ID.

```python
...

    else:

        tracking_objects_copy = tracking_objects.copy()

        for object_id, pt2 in tracking_objects_copy.items():
            object_exists = False
            for pt in center_points_cur_frame_copy:
                distance = math.hypot(pt2[0] - pt[0], pt2[1] - pt[1])

                # Update IDs position
                if distance < 20:
                    tracking_objects[object_id] = pt
                    object_exists = True
                    continue

            # Remove IDs lost
            if not object_exists:
                tracking_objects.pop(object_id)
...
```

Now the tracking works quite well and as you can see from the image below, the white car has lost track because the object has not been identified anymore.

<a href="https://ibb.co/wrqyRHh"><img src="https://i.ibb.co/mFKJGs5/object-tracking-from-scratch-opencv-and-python-lost-ID-1024x601.png" alt="object-tracking-from-scratch-opencv-and-python-lost-ID-1024x601" border="0"></a>

### Add new ID to new cars

If a new object is identified, the list of points must also be updated. So here are the changes of the previous code that allow you to delete the old and add the points of the new cars identified.

```python
...

    else:
        tracking_objects_copy = tracking_objects.copy()
        center_points_cur_frame_copy = center_points_cur_frame.copy()

        for object_id, pt2 in tracking_objects_copy.items():
            object_exists = False
            for pt in center_points_cur_frame_copy:
                distance = math.hypot(pt2[0] - pt[0], pt2[1] - pt[1])

                # Update IDs position
                if distance < 20:
                    tracking_objects[object_id] = pt
                    object_exists = True
                    if pt in center_points_cur_frame:
                        center_points_cur_frame.remove(pt)
                    continue

            # Remove IDs lost
            if not object_exists:
                tracking_objects.pop(object_id)

        # Add new IDs found
        for pt in center_points_cur_frame:
            tracking_objects[track_id] = pt
            track_id += 1
...
```

As can be seen from the image below, the id is kept until the object is recognized. In fact, in the central part of the video, it traces very well.

<a href="https://ibb.co/Fn39gbb"><img src="https://i.ibb.co/mSTm6CC/image.png" alt="image" border="0"></a>

# Contribute

If you come across any errors or bugs, please don't hesitate to reach out via email, or feel free to create an issue ticket. Also, if you have any ideas or suggestions for enhancements, we'd love to hear from you. Your feedback and contributions are invaluable in improving this resource. Thank you for your collaboration in making this even better! 😊

</div>
