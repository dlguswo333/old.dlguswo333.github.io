---
layout: post
toc: true
math: true
title: "k Means Clustering"
categories: 
tags: [Algorithms, Python]
author:
  - 이현재
---

<!-- image (unclustered->clustered) -->
**k means clustering** divides arbitrary data into groups
based on their similarity (or distance) into ***k*** clusters.
The algorithm is effective at grouping unclustered data
without any difficulty, but the result is not uniform (randomness)
and it may require some time.
<!--more-->

However, the result will be acceptable if the data pattern can be
well-categorized and the algorithm is easy to understand and implement.

# 1. How Does it Work?
First of all, the data need to be able to produce similarities between them,
such as in forms of distance. One example is a vector of *(1 X N)*.
a vector *A* and a vector *B*, their distance can be calculated with
Euclidean distance or Manhattan distance.
A 2D coordinates is also a vector of *(1 X N)*.
$$
Euclidean\ Distance: \sqrt{\sum_{i=1}^N{(A_i-B_i)^2}}
\\
Manhattan\ Distance: \sum_{i=1}^N{|A_i-B_i|}
$$

To describe keypoints, k means algorithms works as below:
1. Select initial centroids randomly.
2. Map each vector to the neareest centroid and cluster.
3. Calculate the next centroids by averaging the vectors within each cluster.
4. Repeat 2-3 until no changes further.

<!-- image (step 1-4) -->

## 1) Select Initial Centroids Randomly.
Since we have no information on the data, we randomly select
*k* vectors and call them centroids. We are skipping better implementations
on how to do this, but it will be much more effective to select vectors
that can represent each cluster. How don't you try to select vectors which
are as much far from each other as you can within negligible time?

## 2) Map each Vector to the Neareest Centroid and Cluster.
For each vector, find the nearest centroid and group them together (cluster).
It may not look okay with random initial centroids, but it is okay.
We will get better centroids.

## 3) Calculate the next Centroids by Averaging the Vectors within each Cluster.
For each cluster you acquired, get the average of vectors that reside in
the cluster. The output will represent the vectors in the cluster.
You might have noticed that the outputs show the clusters better than
the previous clusters do. Replace the centroids with the average vectors.

## 4) Repeat 2-3 until no Change further.
Repeat step 2 and 3 until centroids converge. It may take long if the initial
centroids are not well-chosen or there are too many data so computing the
next centroids is hard work. In that case, you may decide to exit the loop
after some *N* repetition or the differences between the previous and
the next centroids are negligible.

# 2. k Means Clustering in Python Code
{% highlight python linenos %}
def kmeans_cluster(k:int, vectors:List[List[float]])->List[List[List[float]]]:
    ''' Run k means clustering on given vectors and return centroids.
        k must be equal or smaller than the number of vectors.
    '''
    num_vectors=len(vectors)
    vector_len=len(vectors[0])
    assert k<=num_vectors
    # (1) Select initial centroids randomly.
    indices=random.sample(range(num_vectors), k)
    centroids=[vectors[index] for index in indices]
    # This list stores nearest centroid of each row.
    nearest_centroid=[0]*num_vectors

    # Repeat run until converging.
    while True:
        # (2) Map each vector to the nearest centroid.
        for i in range(num_vectors):
            # Get the index of nearest centroid for each vector.
            nearest_centroid[i]=get_nearest_centroid_index(vectors[i], centroids)

        next_centroids=[[0]*vector_len for _ in range(k)]
        nearest_cnt=[0]*k
        # For each centroid, sum all the vector values,
        # which has the centroid as the nearest.
        for i in range(num_vectors):
            next_centroids[nearest_centroid[i]]=add_vector(next_centroids[nearest_centroid[i]], vectors[i])
            nearest_cnt[nearest_centroid[i]]+=1
        # Divide by count to get the average values.
        for i in range(k):
            # Caution! Some centroid may have no vectors,
            # which has the centroid as the nearest.
            # In that case, use the previous centroid value as it is.
            next_centroids[i]=[n_ele/nearest_cnt[i] for n_ele in next_centroids[i]] if nearest_cnt[i]!=0 else centroids[i]
        if next_centroids==centroids:
            break
        centroids=next_centroids
    clusters=cluster_vectors(vectors, centroids)
    return clusters
{% endhighlight %}



# 3. Hierarchical k Means Clustering


# 4. Hierarchical k means Clustering in Python Code