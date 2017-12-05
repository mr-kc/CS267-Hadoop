# CS267-Hadoop
Final Project for CS267



# Running K-means on AWS- EMR cluster



Create directories in HDFS to store the input files using mkdir command
Hadoop fs -mkdir input_data
## Copy the input files from local to HDFS
Hadoop fs -copyFromLocal < text_game_lda_*> <input_data>
## Run seqdirectory command to generate the input file for sequence files to sparse
Mahout seqdirectory \
-i <path to input_data> \
-o <path to store the seqfiles>
-ow
## Run seq2sparse to generate the vector files
Mahout seq2sparse \
-i <path to seqfiles> \
-o <path to vectors> \
-ow
## Seq2sparse generates the following files in the output directory specified:
<path to vectors>/df-count
<path to vectors>/dictionary.file-0
<path to vectors>/frequency.file-0
<path to vectors>/tf-vectors
<path to vectors>/tfidf-vectors
<path to vectors>/tokenized-documents
<path to vectors>/wordcount
## The term frequency vectors generated from the above step can be used to
run canopy clustering to generate initial overlapping clusters that can be
used by k-means to generate the final clusters. This is an optional step.
Running canopy clustering before run any hierarchical clustering methods
speeds up the clutering process of large data-sets.
Mahout canopy -i <path to vectors>/tf-vectors \
-o <output location to canopy clusters> \
-dm org.apache.mahout.common.distance.EucledianDistanceMeasure \
-t1 1500 -t2 2000
## Run k-means now on the initial cluster seeds obtained from canopy
clustering.
Mahout kmeans -i <path to vectors>/tfidf-vectors \
-c <path to initial cluster seeds> \
-o <path to generated kmeans clusters> \
-k 8 -x 500 -ow
## Export the cluster obtained using the cluterdump utility and store the clusters in
kmeans_cluster.txt file
Mahout clusterdump -dt sequencefile \
-d <path to vectors>/dictionary.file-0 \
-i <path to generated kmeans clusters>/cluster-*-final \
-o kmeans_cluster.txt
