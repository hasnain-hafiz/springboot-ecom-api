# Course Finder API

A Spring Boot application that provides powerful course search functionality using Elasticsearch.

---

## 🚀 Quick Start

### 1. Prerequisites

- **Docker & Docker Compose** (for Elasticsearch)
- **Java 23**
- **Maven**
- **curl** (for API testing)

---

### 2. Launch Elasticsearch

Start Elasticsearch with Docker Compose:
```bash
docker-compose up -d
```
Verify it's running:
```bash
curl http://localhost:9200
```
You should see a response similar to:
```json
{
  "name": "elasticsearch-node1",
  "cluster_name": "docker-cluster",
  "version": { "number": "8.x.x", ... }
}
```

---

### 3. Build & Run the Application

```bash
# Build the application
mvn clean package

# Run the application
mvn spring-boot:run
```
The API will start at: [http://localhost:8080](http://localhost:8080)

---

### 4. Configuration

Application configuration (`src/main/resources/application.properties`):
```properties
# Elasticsearch
spring.elasticsearch.uris=http://localhost:9200
spring.elasticsearch.connection-timeout=5s
spring.elasticsearch.socket-timeout=3s

# Application
server.port=8080
```

---

### 5. Data Population

Sample data loads automatically at startup.

To verify:
```bash
# Check index exists
curl http://localhost:9200/courses

# Count documents
curl http://localhost:9200/courses/_count
```
Example count response:
```json
{
  "count": 100,
  "_shards": { "total": 1, "successful": 1, "skipped": 0, "failed": 0 }
}
```

---

## 📝 API Documentation

### Search Endpoint

**Base URL:**  
`GET /api/search/`

#### Query Parameters

| Name           | Type     | Description                                              |
|----------------|----------|----------------------------------------------------------|
| `query`        | string   | Text to search in title and description (optional)       |
| `category`     | string   | Filter by category (optional)                            |
| `type`         | string   | Filter by type: `ONE_TIME`, `COURSE`, `CLUB` (optional)  |
| `minAge`       | int      | Minimum age (optional)                                   |
| `maxAge`       | int      | Maximum age (optional)                                   |
| `minPrice`     | decimal  | Minimum price (optional)                                 |
| `maxPrice`     | decimal  | Maximum price (optional)                                 |
| `sortBy`       | string   | Field to sort by: `price`, `nextSessionDate` (optional)  |
| `sortDirection`| string   | `asc` or `desc` (optional)                               |
| `page`         | int      | Page number (default: 0)                                 |
| `size`         | int      | Results per page (default: 10)                           |

#### Example Requests

- **Basic Search**
    ```bash
    curl "http://localhost:8080/api/search/?query=math"
    ```
- **Filter by Category and Type**
    ```bash
    curl "http://localhost:8080/api/search/?category=STEM&type=COURSE"
    ```
- **Age and Price Range**
    ```bash
    curl "http://localhost:8080/api/search/?minAge=8&maxAge=12&minPrice=50&maxPrice=200"
    ```
- **Sorted Results**
    ```bash
    curl "http://localhost:8080/api/search/?sortBy=price&sortDirection=asc"
    ```
- **Pagination**
    ```bash
    curl "http://localhost:8080/api/search/?page=1&size=20"
    ```
- **Combined Filters**
    ```bash
    curl "http://localhost:8080/api/search/?query=science&category=STEM&type=COURSE&minAge=10&maxPrice=150&sortBy=price&sortDirection=desc&page=0&size=10"
    ```

#### Example Response

```json
{
  "content": [
    {
      "id": 1,
      "title": "Math for Beginners",
      "category": "STEM",
      "type": "COURSE",
      // ...
    }
  ],
  "pageable": {
    "pageNumber": 0,
    "pageSize": 10
  },
  "totalElements": 12,
  "totalPages": 2
}
```

#### Filtering & Sorting

- **Category/Type**: exact match
- **Age/Price**: inclusive range
- **Text Search**: partial matches in title/description
- **Default sorting**: by relevance  
  Custom sort by `price` (numeric) or `nextSessionDate` (chronological)

---

## ✨ Advanced Features

### Autocomplete

The `query` parameter supports autocomplete:
```bash
curl "http://localhost:8080/api/search/?query=mat"
```
Returns matches like "math", "mathematics", etc.

### Fuzzy Search

Add a tilde (`~`) for fuzzy matching:
```bash
curl "http://localhost:8080/api/search/?query=mathmatics~"
```
Returns close matches, e.g., "mathematics" for typos.

---

## 🛠️ Troubleshooting

- **Elasticsearch not responding:**
    ```bash
    docker ps
    docker logs <container-id>
    ```
- **Application won't start:**
    - Ensure Elasticsearch is running
    - Check `mvn spring-boot:run` logs for errors
    - Make sure port 8080 is free

---

## 👩‍💻 Development

- To modify sample data, edit:  
  `src/main/resources/data/courses.json`
- To rebuild and reload:
    ```bash
    mvn clean package
    mvn spring-boot:run
    ```

---

## 📄 License

MIT (or your chosen license)
