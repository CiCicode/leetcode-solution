# LeetCode SQL Learning Progress
Tracking 50 problems from basic to advanced

```
階段	     狀態	           掌握技能	                  
初級階段	   已完成	  基礎查詢、WHERE條件、NULL處理	
中級階段	   進行中	   JOIN操作、聚合計算、自連接	
高級階段	   待開始	  視窗函數、遞迴查詢、效能優化	
```

### 1757. Recyclable and Low Fat Products 找出同時符合低脂且可回收的產品ID。
**連結**: [LeetCode 1757](https://leetcode.com/problems/recyclable-and-low-fat-products/description/)
#### SQL 解法

```sql
SELECT product_id
FROM Products
WHERE low_fats = 'Y' AND recyclable = 'Y';
```
學習重點:
1. 基本 WHERE 條件
2. AND 運算符使用

### 584. Find Customer Referee 找出推薦人不是2的客戶，包括沒有推薦人的客戶。
**連結**: [LeetCode 584](https://leetcode.com/problems/find-customer-referee/description/)
#### SQL 解法

```sql
SELECT name
FROM Customer
WHERE referee_id IS NULL OR referee_id <> 2;
```
學習重點:
1. NULL 特殊處理
2. OR 運算符使用
3. IS NULL vs = NULL

### 595. Big Countries 找出面積大於300萬或人口大於2500萬的國家。
**連結**: [LeetCode 595](https://leetcode.com/problems/big-countries/description/)
#### SQL 解法

```sql
SELECT name, population, area
FROM World
WHERE area >= 3000000 OR population >= 25000000;
```
學習重點:
1. 數值比較
2. OR 連接多個條件

### 1148. Article Views I 找出看過自己文章的作者ID。
**連結**: [LeetCode 1148](https://leetcode.com/problems/article-views-i/description/)
#### SQL 解法

```sql
SELECT DISTINCT author_id AS id
FROM Views
WHERE author_id = viewer_id
ORDER BY author_id;
```
學習重點:
1. 欄位比較（同一表格內）
2. DISTINCT 去除重複
3. ORDER BY 排序

### 1683. Invalid Tweets 找出內容長度超過15個字元的推文ID。
**連結**: [LeetCode 1683](https://leetcode.com/problems/invalid-tweets/description/)
#### SQL 解法

```sql
SELECT tweet_id
FROM Tweets
WHERE CHAR_LENGTH(content) > 15
ORDER BY tweet_id;
```
學習重點:
1. 字串函數 CHAR_LENGTH()
2. 函數在 WHERE 中的應用

### 1378. Replace Employee ID With The Unique Identifier 顯示每個員工的 unique_id，沒有則顯示 NULL。
**連結**: [LeetCode 1378](https://leetcode.com/problems/replace-employee-id-with-the-unique-identifier/)
#### SQL 解法

```sql
SELECT uni.unique_id, emp.name
FROM Employees AS emp
LEFT JOIN EmployeeUNI AS uni
ON emp.id = uni.id;
```
學習重點:
1. LEFT JOIN 保留所有左表記錄
2. 處理沒有對應記錄的情況

### 1068. Product Sales Analysis I 查詢每個銷售記錄的產品名稱、年份和價格。
**連結**: [LeetCode 1068](https://leetcode.com/problems/product-sales-analysis-i/description/)
#### SQL 解法

```sql
SELECT prod.product_name, sale.year, sale.price
FROM Sales AS sale
INNER JOIN Product AS prod
ON sale.product_id = prod.product_id;
```
學習重點:
1. INNER JOIN 只返回匹配記錄
2. 外鍵保證的數據完整性
3. 多欄位選擇

### 1581. Customer Who Visited but Did Not Make Any Transactions 找出有拜訪但沒有交易的客戶及拜訪次數。
**連結**: [LeetCode 1581](https://leetcode.com/problems/customer-who-visited-but-did-not-make-any-transactions/description/)
#### SQL 解法

```sql
SELECT v.customer_id, COUNT(v.visit_id) AS count_no_trans
FROM Visits AS v
LEFT JOIN Transactions AS t
ON v.visit_id = t.visit_id
WHERE t.transaction_id IS NULL
GROUP BY v.customer_id;
```
學習重點:
1. LEFT JOIN 後過濾 NULL
2. GROUP BY 與 COUNT() 結合

### 197. Rising Temperature 找出溫度比前一天高的日期。
**連結**: [LeetCode 197](https://leetcode.com/problems/rising-temperature/description/)
#### SQL 解法

```sql
SELECT today.id
FROM Weather today
JOIN Weather yesterday
ON today.recordDate = DATE_ADD(yesterday.recordDate, INTERVAL 1 DAY)
WHERE today.temperature > yesterday.temperature;
```
學習重點:
1. 自連接語法（使用不同別名）
2. 日期函數 DATE_ADD()
3. 相鄰日期比較

### 1661. Average Time of Process per Machine 計算每台機器處理每個流程的平均時間。
**連結**: [LeetCode 1661](https://leetcode.com/problems/average-time-of-process-per-machine/description/)
#### SQL 解法

```sql
SELECT start_act.machine_id, ROUND(AVG(end_act.timestamp - start_act.timestamp), 3) AS processing_time
FROM Activity start_act
JOIN Activity end_act
    ON start_act.machine_id = end_act.machine_id
    AND start_act.process_id = end_act.process_id
    AND start_act.activity_type = 'start'
    AND end_act.activity_type = 'end'
GROUP BY start_act.machine_id
ORDER BY start_act.machine_id;
```
學習重點:
1. 多重條件自連接
2. 時間差計算
3. ROUND() 函數應用

### 570. Managers with at Least 5 Direct Reports 找出至少有5個直接下屬的經理姓名。
**連結**: [LeetCode 570](https://leetcode.com/problems/managers-with-at-least-5-direct-reports/description/)
#### SQL 解法

```sql
SELECT e.name
FROM Employee e
JOIN (
    SELECT managerId
    FROM Employee
    WHERE managerId IS NOT NULL
    GROUP BY managerId
    HAVING COUNT(*)>=5
) m ON e.id =m.managerId;
```
學習重點:

1. GROUP BY 分組統計
2. HAVING 分組後過濾
3. WHERE 與 HAVING 的區別
4. 子查詢應用

### 1934. Confirmation Rate 計算每個用戶的電子郵件確認率
**連結**: [LeetCode 1934](https://leetcode.com/problems/confirmation-rate/)
#### SQL 解法

```sql
SELECT s.user_id, ROUND(IFNULL(SUM(CASE WHEN c.action = 'confirmed' THEN 1 ELSE 0 END) / COUNT(c.user_id),0),2) AS confirmation_rate
FROM Signups s
LEFT JOIN Confirmations c
    ON s.user_id = c.user_id
GROUP BY s.user_id;
```
學習重點
1. 條件聚合：`SUM(CASE WHEN ... THEN 1 ELSE 0 END)`
2. 安全除法：`IFNULL(分子/分母, 0)`
3. COUNT() 的 NULL 處理：`COUNT(c.user_id)` vs `COUNT(*)`
4. ROUND() 函數應用

### 620. Not Boring Movies 查找所有有趣的非無聊電影
**連結**: [LeetCode 620](https://leetcode.com/problems/not-boring-movies/)
#### SQL 解法

```sql
SELECT *
FROM Cinema
WHERE id % 2 = 1 AND description !='boring'
ORDER BY rating DESC;
```
學習重點
1. 數學運算符：`%`（取餘數）
2. 字串比較運算符：`!=` 或 `<>`
3. 邏輯運算符：`AND` 組合多條件
4. 排序：`DESC` 降序排列

### 1251. Average Selling Price 計算每個產品的平均售價
**連結**: [LeetCode 1251](https://leetcode.com/problems/average-selling-price/)
#### SQL 解法

```sql
SELECT p.product_id, IFNULL(ROUND(SUM(p.price * u.units) / SUM(u.units), 2), 0) AS average_price
FROM Prices p
LEFT JOIN UnitsSold u
     ON p.product_id = u.product_id
     AND u.purchase_date BETWEEN p.start_date AND p.end_date
GROUP BY p.product_id;
```
學習重點
1. 日期範圍 JOIN：使用 BETWEEN 匹配日期
2. 加權平均計算：SUM(價格×數量) ÷ SUM(數量)
3. LEFT JOIN 保留所有：處理沒有銷售的產品
4. IFNULL 安全計算：處理除以0的情況

### 1075. Project Employees I 查詢每個專案中員工的平均工作年資
**連結**: [LeetCode 1075](https://leetcode.com/problems/project-employees-i/)
#### SQL 解法

```sql
SELECT p.project_id, ROUND(AVG(e.experience_years), 2) AS average_years
FROM Project p
LEFT JOIN Employee e
     ON p.employee_id = e.employee_id
GROUP BY p.project_id;
```
學習重點
1. 使用 LEFT JOIN：確保專案中的所有員工都被計算，即使 Employee 表格中沒有對應記錄（雖然題目說有外鍵，但用 LEFT JOIN 更安全）
2. 計算平均值：AVG(e.experience_years)
3. 四捨五入：ROUND(..., 2) 保留2位小數
4. 分組：GROUP BY p.project_id 按專案分組

### 1633. Percentage of Users Attended a Contest 計算每個比賽的用戶參與百分比
**連結**: [LeetCode 1633](https://leetcode.com/problems/percentage-of-users-attended-a-contest/)
#### SQL 解法

```sql
SELECT r.contest_id, ROUND(COUNT(DISTINCT r.user_id)*100/(SELECT COUNT(DISTINCT user_id) FROM Users), 2) AS percentage
FROM Register r
GROUP BY r.contest_id
ORDER BY percentage DESC, r.contest_id;
```
學習重點
1. 計算總用戶數：使用子查詢 (SELECT COUNT(DISTINCT user_id) FROM Users)
2. 計算百分比：(比賽用戶數 ÷ 總用戶數) × 100
3. 四捨五入：ROUND(..., 2) 保留2位小數
4. 排序：先按百分比降序，再按 contest_id 升序

### 1211. Queries Quality and Percentage 計算每個查詢的：1. 品質 (quality) = 每次查詢評分的平均值 2. 劣質查詢百分比 (poor_query_percentage) = 評分 < 3 的查詢所佔百分比
**連結**: [LeetCode 1211](https://leetcode.com/problems/queries-quality-and-percentage/)
#### SQL 解法

```sql
SELECT query_name, 
       ROUND(AVG(rating*1.0 / position),2) AS quality,
       ROUND(SUM(CASE WHEN rating < 3 THEN 1 ELSE 0 END)*100 / COUNT(*),2) AS poor_query_percentage
FROM Queries
GROUP BY query_name;
```
學習重點
1. CASE WHEN 在聚合函數中的應用
2. 轉換為浮點數的技巧（* 1.0)
3. 在單一查詢中計算不同類型的指標
4. 四捨五入：ROUND(..., 2) 保留2位小數
5. GROUP BY 時，所有的聚合函數（SUM、COUNT、AVG）都只作用在「當前分組」的資料上。

