# leetcode-solution
My LeetCode solutions with detailed explanations

## 類別 1：基礎 WHERE 條件過濾

### 1757. Recyclable and Low Fat Products 找出同時符合低脂且可回收的產品ID。
**連結**: [https://leetcode.com/problems/recyclable-and-low-fat-products/description/]
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
**連結**: [https://leetcode.com/problems/find-customer-referee/description/]
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
**連結**: [https://leetcode.com/problems/big-countries/description/]
#### SQL 解法

```sql
SELECT name, population, area
FROM World
WHERE area >= 3000000 OR population >= 25000000;
```
學習重點:
1. 數值比較
2. OR 連接多個條件

## 類別 2：進階條件與函數應用

### 1148. Article Views I 找出看過自己文章的作者ID。
**連結**:[https://leetcode.com/problems/article-views-i/description/]
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
**連結**:[https://leetcode.com/problems/invalid-tweets/description/]
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

## 類別 3：JOIN 操作基礎

### 1378. Replace Employee ID With The Unique Identifier 顯示每個員工的 unique_id，沒有則顯示 NULL。
**連結**:[https://leetcode.com/problems/replace-employee-id-with-the-unique-identifier/]
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
**連結**:[https://leetcode.com/problems/product-sales-analysis-i/description/]
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

## 類別 4：LEFT JOIN + 條件過濾

### 1581. Customer Who Visited but Did Not Make Any Transactions 找出有拜訪但沒有交易的客戶及拜訪次數。
**連結**:[https://leetcode.com/problems/customer-who-visited-but-did-not-make-any-transactions/description/]
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

## 類別 5：自連接（SELF JOIN）

### 197. Rising Temperature 找出溫度比前一天高的日期。
**連結**: [https://leetcode.com/problems/rising-temperature/description/]
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
**連結**:[https://leetcode.com/problems/average-time-of-process-per-machine/description/]
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

## 類別 6：聚合計算與 HAVING

### 570. Managers with at Least 5 Direct Reports 找出至少有5個直接下屬的經理姓名。
**連結**: [https://leetcode.com/problems/managers-with-at-least-5-direct-reports/description/]
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

## 類別 7：條件聚合與安全計算

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

