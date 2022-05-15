# Introduction

## What is computer vision?
Computer vision is the automatic extraction of "meaningful" information from images and videos.

<img 
style="display: block; 
        margin-left: auto;
        margin-right: auto;
        width: 80%;"
src="../img/p13.png">
</img>

## Why study computer vision?
- Relieve humans of boring, easy tasks
- Enhance human abilities: human-computer interaction, visualization, augmented reality (AR)
- Perception for autonomous robots
- Organize and give access to visual content

## Vision in Humans
- Vision is our most powerful sense. Half of primate cerebral cortex is devoted to visual processing.
- The retina is ~1,000 mm^2. Contains 130 million photoreceptors (120 mil. rods for low light vision and 10 mil. cones for color sampling) covering a visual field of 220x135 degrees.
- Provides enormous amount of information: data-rate of ~3 GBytes/s.
- To match the eye resolution, we would need a 500 Megapixel camera. But in practice the acuity of an eye is 8 Megapixels within a 18-degree field of view (5.5 mm diameter) around a region called fovea.

### How We See
- The area we see in focus and in full color represents the part of the visual field that is covered by the fovea.
- The fovea is 0.35 mm in diameter, covers a visual field of 1-2 degrees, has high density of cone cells.
- Within the rest of the peripheral visual field, the image we perceive becomes more blurry (rod cells)

<img 
style="display: block; 
        margin-left: auto;
        margin-right: auto;
        width: 80%;"
src="../img/p19.png">
</img>

## Origins of Computer Vision
- 1963 - L. G. Roberts publishes his PhD thesis on Machine Perception of Three Dimensional Solids, thesis, MIT Department of Electrical Engineering
    - He is the inventor of ARPANET, the current Internet
- 1966 – Seymour Papert, MIT, publishes the Summer Vision Project asking students to design an algorithm to segment an image into objects and background… within one summer!
- 1969 – David Marr starts developing a framework for processing visual information

## Computer Vision Vs. Computer Graphics
The two problems are inverted: analysis vs. synthesis

<img 
style="display: block; 
        margin-left: auto;
        margin-right: auto;
        width: 40%;"
src="../img/p24.png">
</img>

## Reference Textbooks
- Computer Vision: Algorithms and Applications, by Richard Szeliski, 2009. PDF freely downloadable from the author webpage: <http://szeliski.org/Book/>
- Chapter 4 of Autonomous Mobile Robots, by R. Siegwart, I.R. Nourbakhsh, D. Scaramuzza [PDF](https://rpg.ifi.uzh.ch/docs/teaching/2020/Ch4_AMRobots.pdf)
- Alternative books:
    - Robotics, Vision and Control: Fundamental Algorithms, by Peter Corke 2011.
    - An Invitation to 3D Vision: Y. Ma, S. Soatto, J. Kosecka, S.S. Sastry
    - Multiple view Geometry: R. Hartley and A. Zisserman