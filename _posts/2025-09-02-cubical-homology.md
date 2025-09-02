# Cubical Homology: Intuition and Application

If you’re curious about how TDA can be applied in image processing or classification, you’ve probably noticed that there are simply not enough resources—barely any at all—on how to learn this “secret skill.” I ran into this problem firsthand and found that there are really only two main ways to learn Cubical Homology:

1. Read a book like Computational Homology by Tomasz Kaczynski, Konstantin Mischaikow, and Marian Mrozek, which includes a chapter on Cubical Homology.

2. Dig through research articles that apply the concept.

The first way is great if you enjoy torturing yourself with lots of math, analysis, and topology—only to end up realizing how clueless you are. The second way requires painstakingly collecting knowledge inch by inch from papers, which feels like the dream life of an office clerk.

I also tried approaching this by doing coding projects—learning through trial, error, and frustration, sometimes feeling like I was making my hands bleed and bruising my head from banging it against the desk when nothing worked. Needless to say, this had its ups and downs.

In this article, I’ll try to give an intuitive, visual introduction to Cubical Homology and show how it can be applied to a simple image classification task using machine learning. Along the way, I’ll point out spots where you can experiment with your own classification approaches, though I won’t dive into too much detail just yet.

So — let the fun begin!

## A Quick Recap: Persistent Homology on Point Clouds

The core idea of **Topological Data Analysis (TDA)** is to extract *topological cavities*—such as connected components and holes.  
At this stage, I’ll assume you’re already somewhat familiar with **Persistent Homology**, at least briefly.  

To recap briefly: given a point cloud, you place a ball around each point, using a chosen distance metric (commonly the Euclidean distance).  
You then vary the radius (threshold) $\epsilon$. When $n$ balls around $n$ points intersect pairwise, they form an $n$-dimensional **Vietoris–Rips simplex**.  

Each time new simplices are created, you compute homology (using efficient algorithms such as Gaussian matrix elimination).  
You then record the **birth** and **death** of each topological cavity (connected component or hole) in the form of:  

- **Persistence barcodes**  
- **Persistence diagrams**  

This is a very short reminder of how things work in the basic point-cloud setting.

## Cubical Homology

A similar idea can be applied to images. We can extract data from an image in the form of a point cloud. The most straightforward way is to treat each pixel as a point in $\mathbb{R}^3$, with coordinates $(x, y, z)$, where $(x, y)$ is the position of the pixel on the grayscale image grid (for example, $256 \times 256$ or $1024 \times 1024$ for high-resolution images), and $z$ is the intensity of the grayscale value, ranging from $0$ (black) to $256$ (white).

The problem with this approach is that the resulting point cloud can easily reach sizes of $60{,}000$ points—or even over $1$ million for high-resolution images. With such large point clouds, the computational cost of constructing Vietoris–Rips complexes grows exponentially.

Of course, there are some clever tricks to simplify or filter the point cloud during extraction, but an even better approach is to consider pixels in their natural form: as squares. From there, simplicial complexes give way to cubical complexes. 

Recall that we call an **$n$-simplex** is the convex hull of $(n + 1)$ points, where each point is connected to every other point, and the shape lives in $\mathbb{R}^m$ dimension, where $m > n$. A **simplicial complex** is a collection of simplicies such that:
- every face is included: if the simplex is in the complex, than all its faces (lower dimesnion simplicies) are also included
- simplicies intersect nicely: the intersection of any two simplices is either empty, or it’s a face of both.

Again, we ommit any formal maths definitions for now, just building a visual intuition. We now are going to introduce **Cubical Complex**. 

If you imagine a coordinate system in $\mathbb{R}^n$ and place points on the coordinate axes at unit distance from the origin (for example, in $\mathbb{R}^3$ the points are $(1,0,0)$, $(0,1,0)$, and $(0,0,1)$), then:  

- An **$n$-simplex** is obtained by connecting those points pairwise.  
- An **$n$-cube** is obtained by forming the hypercube with those points.  

For example:  

- A **$0$-simplex** and a **$0$-cube** both represent a single point in $0$D.  
- A **$1$-simplex** and a **$1$-cube** both represent a line segment in $1$D.  
- A **$2$-simplex** is a triangle, whereas a **$2$-cube** is a square in $2$D.  
- A **$3$-simplex** is a tetrahedron, whereas a **$3$-cube** is a cube in $3$D.  



![Simplices and Cubes]({{ "/assets/images/simplicies and cubes.png" | relative_url }})

In a similar fashion we define  **cubical complex**, which is a collection of simplicies such that every face is included and cubes intersect nicely. 

That's great, cause now we can actually **apply** this idea in image analysis. Remeber that each image is a grid of coloured pixels, and pixel is a natural $2$-cube. If we consider a binary image, where each pixel is either white or black, we just created a perfect example of a **cubical complex**, collection of cubes where an intersection between any two is either a point ($0$-cube) or an edge ($1$-cube). Similar strategy can be applied in a $3$D image (MRI, CT etc), where each *voxel* ($3$D pixel) is a $3$-cube. 

Below you can see a binary image which represents a cubical complex. You can clearly see its homology: 4 connected components ($0$-dimension homology) and 3 holes ($1$-dimension homology). For visual purpose, I coloured the connected components and holes seperately. 

![Diagram of Binary Image]({{"assets/images/Binary image and homology.png" | relative_url}})

To some extent, a cubical complex is simpler than a simplicial complex. It is called a Cruel Irony, just like our obsession with topology (just why). In cubical homology, one pixel can only be connected to a maximum of 8 pixels, either via an edge or a vertex. In contrast, in the case of a point cloud, each point can be connected to as many points as the size of the cloud. For an image, this number can go up to 60,000, as we established earlier. So you risk putting your laptop on fire trying to compute Vietoris-Rips complexes (and don’t ask me how I know).

## Filtration functions

We can calculate cubical homology when we have a binary image, so all pixels are either black or white. Obviously, in the real world we rarely have binary images. This is why we introduce a filtration function, which essentially filters the pixels we want to keep from those we don’t.

Let me explain. In a basic point cloud situation, we create a metric ball around each point and look for the pairwise intersections to construct a Vietoris-Rips complex at each threshold. We essentially generate a sequence of subsets of simplicial complexes. If we look at the sequence from the end — when everything is connected — we are essentially filtering this massive simplicial complex by “deleting” some of the edges. That’s filtration.

Something even simpler happens with cubical homology. Remember that each pixel is a $2$-cube. By choosing which pixels to keep at each step, we are already performing a form of filtration.

Note the difference: in simplicial homology, we keep all points and create increasingly complex simplices at each step. In cubical homology, we start with a small number of $2$-cubes (pixels) and add more with each step, simultaneously creating a different cubical complex. So much *simpler* than Simplicial Complex. 

Now we can turn our attention to how we can choose which pixels to keep and which to discard. There are several different methods, and you can even create your own, but in this article I won’t delve into the details. The most basic and versatile method is to look at the gray intensity.

First, the image has to be converted to grayscale. You can cheat a bit by extracting the RGB channels from the image and then converting each into grayscale, but here we will focus on a simple gray image. Each pixel is now represented by a number corresponding to its gray intensity, either from $0$ to $1$ (where $0$ is white and $1$ is black) or from $0$ to $255$ (where $0$ is white). This is great news because now we can create a clear, continuous filtration function.

Without diving too deep into math, the basic idea is:

1. Choose a threshold value.

2. Each pixel with a grayscale value greater than this threshold is mapped to $1$ (or $255$ in another metric).

3. Each pixel with a grayscale value less than this threshold is mapped to $0$.

Congratulations! You have just created a binary image from the original one. By varying the threshold value, we can add (or delete) more pixels each time. As I explained earlier, this allows us to intuitively see the homology from a binary image.

The image below illustrates this basic technique on a simple grayscale image, and how the Cubical Complex changes with each threshold. 


