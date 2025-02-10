## **Programming Assignment 2 Documentation**

### **Overview**
This project implements a **Lucene-based search engine** that supports **spatial filtering**. The system allows users to search for items based on textual queries and restrict results based on **geographical coordination** using a **bounding box and precise distance filtering**. The backend uses **MySQL** for storing item data, which is indexed using Apache Lucene.

- **Lucene Full-Text Search**: Users can perform keyword searches across item names, descriptions, and categories.
- **Geospatial Filtering**: Results can be restricted to a circular area based on longitude, latitude, and search radius.
- **Efficient Bounding Box Optimization**: Uses a bounding box pre-filter to quickly eliminate distant items.
- **SQL Integration**: Retrieves item data from a MySQL database and indexes it in Lucene.
- **Optimized String Handling**: Uses `StringBuilder` for query construction and result formatting to improve performance.
- **Deduplication of Items Across Categories**: Ensures each item appears only once even if it belongs to multiple categories.
- **Sorting by Relevance, Distance, and Price**: Results are ranked by search relevance, then distance, then price.

---

## **📂 Project Structure**
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

---

## **⚙️ Setup & Usage**
### **1️⃣ Running the Setup**
```bash
chmod +x runLoad.sh
./runLoad.sh
```
This will:
- Start MySQL
- Drop and recreate spatial indexes
- Compile `Indexer.java` and `Searcher.java`
- Index all data into Lucene

### **2️⃣ Searching for Items**
**Basic Search:**
```bash
java Searcher "superman" 10
```
Returns the top 10 results matching "superman".

**Spatial Search (Longitude, Latitude, Radius in km):**
```bash
java Searcher "superman" 10 -x 12.34 -y 56.78 -w 10
```
Only returns results **within 10 km** of the given coordinates.

---

## **🔍 Key Challenges & Solutions**
### **1️⃣ Docker & MySQL Connection Issues**
🔹 **Problem:** The `jdbc:mysql://localhost:3306/ad` connection failed with **"No suitable driver found"**.  
✅ **Solution:** We explicitly added `mysql-connector-java.jar` to the classpath.

### **2️⃣ Lucene Version Compatibility Issues**
🔹 **Problem:** `IndexSearcher.doc(int)` was **deprecated in Lucene 8+**, causing compilation errors.  
✅ **Solution:** We replaced it with:
```java
Document doc = indexSearcher.storedFields().document(hit.doc);
```

### **3️⃣ Preventing Duplicate Results from Multiple Categories**
🔹 **Problem:** Items belonging to multiple categories were **appearing multiple times** in search results.  
✅ **Solution:** We used **a HashSet** to ensure each `ItemId` appears **only once**.

### **4️⃣ Optimizing String Concatenation in SQL Queries & Logging**
🔹 **Problem:** Using `+` for concatenating long SQL queries and log messages was inefficient.  
✅ **Solution:** Replaced with `StringBuilder.append()`:
```java
StringBuilder query = new StringBuilder();
query.append("SELECT it.ItemId, it.Name FROM Items it ")
     .append("INNER JOIN Categories ca ON it.ItemId = ca.ItemId ")
     .append("WHERE ca.Category = 'Books'");
```

### **5️⃣ Implementing Efficient Geospatial Filtering**
🔹 **Problem:** Initially, every document had to be checked against the **Haversine formula**, which was slow.  
✅ **Solution:** We **pre-filtered results using a bounding box**, reducing unnecessary distance calculations.

### **6️⃣ Fixing Bounding Box Edge Cases**
🔹 **Problem:** Our bounding box calculations **broke** when crossing the **±180° longitude** or **±90° latitude**.  
✅ **Solution:** We applied **wrapping logic**:
```java
if (minLon < -180.0) minLon += 360.0;
if (maxLon > 180.0) maxLon -= 360.0;
```

### **7️⃣ Formatting Search Results (Decimals)**
🔹 **Problem:** Distance and score values had too many decimal places.  
✅ **Solution:** Used `DecimalFormat` to **round to two decimal places**:
```java
DecimalFormat df = new DecimalFormat("#.##");
System.out.println("Distance: " + df.format(distance) + " km");
```

---

## **🛠️ Lessons Learned**
✅ **Understanding Lucene internals**: Indexing, querying, and filtering techniques.  
✅ **Optimizing MySQL queries**: Avoiding redundant queries and improving index performance.  
✅ **Geospatial search implementation**: Using **bounding boxes** and **great-circle distance calculations**.  
✅ **String performance optimizations**: Using `StringBuilder` instead of `+` in loops.  
✅ **Debugging Java application in Docker**: Resolving classpath and MySQL connection issues.  

---

## **📌 Future Improvements**
🔹 **Implement caching**: Store frequent queries to speed up responses.  
🔹 **Multi-threaded indexing**: Speed up indexing for large datasets.  
🔹 **Improve category search**: Support **AND/OR** logic for categories.  
🔹 **Refine ranking system**: Tune Lucene similarity scoring for better ranking.  

---

## **👨‍💻 Contributors**
- **[Your Name]**
- **Your Teammates (if applicable)**

---

## **📜 References**
- [Lucene Documentation](https://lucene.apache.org/core/)
- [MySQL Spatial Extensions](https://dev.mysql.com/doc/refman/8.0/en/spatial-types.html)
- [Haversine Formula](https://en.wikipedia.org/wiki/Haversine_formula)

---

## **🛠️ Final Thoughts**
This assignment was **challenging** but rewarding. We faced **many obstacles**, from **Docker networking issues** to **geospatial filtering challenges**, but through **debugging, research, and testing**, we successfully built a **highly optimized** Lucene-based search system! 🚀  
```

---

### **📌 Why This README.md is Useful?**
✔ **Clear documentation** for setting up and using the project.  
✔ **Lists all key challenges and solutions** (showing effort and debugging process).  
✔ **Includes performance optimizations** (bounding box, category deduplication, `StringBuilder`).  
✔ **Summarizes lessons learned** and **future improvements** for better grading.  

This README **not only documents the project** but also **highlights all the efforts and improvements** you made. Let me know if you’d like any refinements! 🚀😊