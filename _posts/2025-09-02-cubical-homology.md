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
At this stage, I’ll assume you’re already somewhat familiar with **Persistent Homology** and how to extract it from a point cloud.  

To recap briefly: given a point cloud, you place a ball around each point, using a chosen distance metric (commonly the Euclidean distance).  
You then vary the radius (threshold) $\epsilon$.  

When $n$ balls around $n$ points intersect pairwise, they form an $n$-dimensional **Vietoris–Rips simplex**.  

Each time new simplices are created, you compute homology (using efficient algorithms such as Gaussian matrix elimination).  
You then record the **birth** and **death** of each topological cavity (connected component or hole) in the form of:  

- **Persistence barcodes**  
- **Persistence diagrams**  

This is a very short reminder of how things work in the basic point-cloud setting.

## Cubical Homology

A similar idea can be applied to images. We can extract data from an image in the form of a point cloud. The most straightforward way is to treat each pixel as a point in $\mathbb{R}^3$, with coordinates $(x, y, z)$, where $(x, y)$ is the position of the pixel on the grayscale image grid (for example, $256 \times 256$ or $1024 \times 1024$ for high-resolution images), and $z$ is the intensity of the grayscale value, ranging from $0$ (black) to $256$ (white).

The problem with this approach is that the resulting point cloud can easily reach sizes of $60{,}000$ points—or even over $1$ million for high-resolution images. With such large point clouds, the computational cost of constructing Vietoris–Rips complexes grows exponentially.

Of course, there are some clever tricks to simplify or filter the point cloud during extraction, but an even better approach is to consider pixels in their natural form: as squares. From there, simplicial complexes give way to cubical complexes.
