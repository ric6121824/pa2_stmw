## **Programming Assignment 2 Documentation**

### **Overview**
This assigned project requires and implements a **Lucene-based search engine** that supports **spatial filtering**. The system allows users to search for items based on textual queries and restrict results based on **geographical coordination** using a **bounding box and precise distance filtering**. The backend uses **MySQL** for storing item data, which is indexed using Apache Lucene.

## **Team**
- **Ping-Chung Tsai** (ptsai@uni-bremen.de) 6361331
- **Md Azijul Islam** (mdazijul@uni-bremen.de) 6337733

**GitHub Repository**: https://github.com/ric6121824/pa2_stmw
---

## **Project Structure**
```
/stmw/student_workspace/
│── createSpatialIndex.sql  # Create Spatial Index in MySQL
│── dropSpatialIndex.sql    # Delete Spatial Index if already exist to avoid conflicts.
│── Indexer.java            # Indexes MySQL data into Lucene
│── Searcher.java           # Performs keyword + spatial searches
│── runLoad.sh              # Script to initialize MySQL, compile, and run indexing
│── README.md               # Project documentation
│── team.txt                # team members infos
```

## **Key Challenges & Solutions**
### **1. Docker & MySQL Connection Issues**
 **Problem:** The **NoSuchFileException** or **AlreadyClosedException** occurs while creating the temporary index directory, whether using the `FSDirectory` with `NoLockFactory`, `MMapDirectory`, or `NIOFSDirectory` with `NoLockFactory`.
 **Solution:** Run the docker under Windows 10 instead of MacOS.

### **2. Preventing Duplicate Results from Multiple Categories**
 **Problem:** Items belonging to multiple categories were **appearing multiple times** in search results.  
 **Solution:** We used **a StringBuilder** to combine all the `Categories` an item has into one string.

### **3. Fixing Bounding Box Edge Cases**
 **Problem:** Our bounding box calculations **broke** when crossing the **±180° longitude** or **±90° latitude**.  
 **Solution:** We applied **wrapping logic**:
```java
if (minLon < -180.0) minLon += 360.0;
if (maxLon > 180.0) maxLon -= 360.0;
```



