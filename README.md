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

### 1211. Queries Quality and Percentage 計算每個查詢的：1. 品質 = 每次查詢評分的平均值 2. 劣質查詢百分比 = 評分 < 3 的查詢所佔百分比
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

### 1193: Monthly Transactions I 編寫一個 SQL 查詢來尋找每個月份和每個國家/地區的交易次數及其總金額、已批准的交易次數及其總金額。
**連結**: [LeetCode 1211](https://leetcode.com/problems/monthly-transactions-i/)
#### SQL 解法

```sql
SELECT DATE_FORMAT(trans_date, '%Y-%m') AS month,
       country,
       COUNT(*) AS trans_count,
       SUM(CASE WHEN state = 'approved' THEN 1 ELSE 0 END) AS approved_count,
       SUM(amount) AS trans_total_amount,
       SUM(CASE WHEN state = 'approved' THEN amount ELSE 0 END) AS approved_total_amount
FROM Transactions
GROUP BY month, country;
```
學習重點
1. DATE_FORMAT(trans_date, '%Y-%m')：將日期轉換為 YYYY-MM 格式，例如 2018-12-18 → 2018-12
2. 在聚合函數中使用 CASE 表達式實現條件計數或求和

### 1174: Immediate Food Delivery II 寫一個SQL查詢，以計算「第一個訂單」中「即時訂單」所佔的百分比，結果得到兩個小數。
**連結**: [LeetCode 1174](https://leetcode.com/problems/immediate-food-delivery-ii/)
#### SQL 解法

```sql
SELECT ROUND(AVG(CASE WHEN order_date = customer_pref_delivery_date THEN 1 ELSE 0 END)*100, 2) AS immediate_percentage
FROM Delivery
WHERE (customer_id, order_date) IN 
(SELECT customer_id, MIN(order_date) FROM Delivery
GROUP BY customer_id);
```
學習重點
1. 子查詢：WHERE (col1, col2) IN (subquery)
2. 找最早日期：MIN(order_date)
3. 在聚合函數中使用 CASE 表達式實現條件計數或求和

### 550: Game Play Analysis IV 寫一個SQL查詢，報告在首次登入的第二天再次登入的玩家的比率，四捨五入到小數點後兩位。
**連結**: [LeetCode 550](https://leetcode.com/problems/game-play-analysis-iv/)
#### SQL 解法

```sql
SELECT 
     ROUND(COUNT(DISTINCT a.player_id) * 1.0 / 
        (SELECT COUNT(DISTINCT player_id) FROM Activity),
        2) AS fraction
FROM Activity a
WHERE (a.player_id, a.event_date) IN (
    SELECT 
        player_id,
        DATE_ADD(MIN(event_date), INTERVAL 1 DAY) AS next_day_should_login
        FROM Activity
    GROUP BY player_id
);
```
```
子查詢結果:
SELECT 
    player_id,
    DATE_ADD(MIN(event_date), INTERVAL 1 DAY) AS next_day_should_login
FROM Activity
GROUP BY player_id;
子查詢結果集（稱為集合B）：
+-----------+----------------------+
| player_id | next_day_should_login |
+-----------+----------------------+
| 1         | 2016-03-02          | ← 玩家1首次登錄(03-01)的次日
| 2         | 2017-06-26          | ← 玩家2首次登錄(06-25)的次日
| 3         | 2016-03-03          | ← 玩家3首次登錄(03-02)的次日
+-----------+----------------------+

FROM Activity a1 -> 主查詢原始數據集（稱為集合A）：
+-----------+------------+
| player_id | event_date |
+-----------+------------+
| 1         | 2016-03-01 |
| 1         | 2016-03-02 | ← 這個會匹配到！
| 1         | 2016-05-02 |
| 2         | 2017-06-25 |
| 3         | 2016-03-02 |
| 3         | 2018-07-03 |
+-----------+------------+

WHERE IN 條件：求交集 -> 集合A ∩ 集合B =
+-----------+------------+
| player_id | event_date |
+-----------+------------+
| 1         | 2016-03-02 | ← 只有這一條記錄！
+-----------+------------+

從交集結果中統計：
COUNT(DISTINCT a1.player_id)=1
總玩家數:
Activity表中的玩家：
1, 2, 3
COUNT(DISTINCT player_id) = 3
```

### 2356. Number of Unique Subjects Taught by Each Teacher 寫一個 SQL 查詢來查詢每位老師所教的唯一科目的數量。
**連結**: [LeetCode 2356](https://leetcode.com/problems/number-of-unique-subjects-taught-by-each-teacher/)
#### SQL 解法

```sql
SELECT teacher_id, COUNT(DISTINCT subject_id) AS cnt
FROM Teacher
GROUP BY teacher_id;
```

### 1141: User Activity for the Past 30 Days I 撰寫一個SQL查詢，找出截至2019-07-27（包含）的30天內每位使用者的每日活躍使用者數。
**連結**: [LeetCode 1141](https://leetcode.com/problems/user-activity-for-the-past-30-days-i/)
#### SQL 解法

```sql
SELECT activity_date AS day, COUNT(DISTINCT user_id) AS active_users
FROM Activity
WHERE activity_date BETWEEN DATE_SUB('2019-07-27', INTERVAL 29 DAY) 
                        AND '2019-07-27'
GROUP BY activity_date;
```
```
原始數據過濾後：
user_id | activity_date
--------|---------------
1       | 2019-07-20    ← v
1       | 2019-07-20    ← v
1       | 2019-07-20    ← v
2       | 2019-07-20    ← v
2       | 2019-07-21    ← v
2       | 2019-07-21    ← v
3       | 2019-07-21    ← v
3       | 2019-07-21    ← v
3       | 2019-07-21    ← v
4       | 2019-06-25    ← ❌ 不在範圍内（早於2019-06-28）
4       | 2019-06-25    ← ❌ 不在範圍内
```

學習重點:
日期範圍篩選：使用BETWEEN, DATE_SUB
給訂截止日期：2019-07-27, 需要過去30天（包含今天）：2019-06-28 到 2019-07-27, 因為要包含今天(2019-07-27)，所以往前推29天(2019-06-28)。

### 1070: Product Sales Analysis III 寫一個SQL查詢，選擇每個產品的**第一年**的 product_id、first_year、quantity 和 price。
**連結**: [LeetCode 1070](https://leetcode.com/problems/product-sales-analysis-iii/)
#### SQL 解法   

```sql
-- 主查詢的 SELECT（外層）：
SELECT product_id, 
       year AS first_year, -- 來自 Sales 表的 year
       quantity, 
       price
FROM Sales
WHERE (product_id, year) IN(
    SELECT -- 子查詢的 SELECT（內層）：
    product_id,
    MIN(year) AS first_year -- 計算出的列，別名為 first_year
    FROM Sales
    GROUP BY(product_id)
);
```

### 596: Classes More Than 5 Students 寫一個SQL查詢，列出所有學生數量至少為5的班級。
**連結**: [LeetCode 596](https://leetcode.com/problems/classes-with-at-least-5-students/)
#### SQL 解法   

```sql
SELECT class
FROM Courses
GROUP BY class
HAVING COUNT(DISTINCT student)>=5;
```
學習重點:
1. WHERE 子句中「不能直接」使用聚合函數，但可以「間接」通過子查詢使用。
2. HAVING 是用於在 GROUP BY 分組後，對整個組（而不是單行）進行過濾的條件子句。

### 1729: Find Followers Count 編寫一個SQL查詢，對於每個用戶，傳回該用戶的追蹤者數量。
**連結**: [LeetCode 1729](https://leetcode.com/problems/find-followers-count/)
#### SQL 解法   

```sql
SELECT user_id, COUNT(DISTINCT follower_id) AS followers_count
FROM Followers
GROUP BY user_id
ORDER BY user_id;
```
### 619: Biggest Single Number 寫一個SQL查詢，找出只出現一次的數字中最大的一個。如果沒有隻出現一次的數字，則輸出 null。
**連結**: [LeetCode 619](https://leetcode.com/problems/biggest-single-number/)
#### SQL 解法

```sql
SELECT MAX(num) AS num
FROM (SELECT num 
      FROM MyNumbers
      GROUP BY num
      HAVING COUNT(*)=1) AS t --把結果看作一個叫做 t 的"臨時表"
; 
```
FROM (SELECT...) AS的結果: 
```
num
---
1    ← 只出現一次
4    ← 只出現一次
5    ← 只出現一次
6    ← 只出現一次
```
學習重點:
FROM 可以接查詢結果，不一定是原始表

### 1045: Customers Who Bought All Products 寫一個SQL查詢，找出購買了所有產品的客戶。
**連結**: [LeetCode 1045](https://leetcode.com/problems/customers-who-bought-all-products/)
#### SQL 解法

```sql
SELECT c.customer_id
FROM Customer c
GROUP BY c.customer_id
HAVING COUNT(DISTINCT c.product_key) = (
    SELECT COUNT(DISTINCT product_key) 
    FROM Product
);
```
學習重點:
1. 子查詢在 HAVING 階段執行
2. 這裡不需要使用JOIN, 用子查詢比較簡潔。

### LeetCode 1731 編寫一個查詢，找出所有至少有1個下屬的經理。
**連結**: [LeetCode 1731](https://leetcode.com/problems/the-number-of-employees-which-report-to-each-employee/)
#### SQL 解法

```sql
SELECT m.employee_id AS employee_id,
       m.name, 
       COUNT(e.employee_id) AS reports_count,
       ROUND(AVG(e.age), 0) AS average_age
FROM Employees m
JOIN Employees e ON m.employee_id = e.reports_to
GROUP BY m.employee_id, m.name
ORDER BY m.employee_id;
```
```
自連接：將表連接到自己
把同一個表想像成兩份copy：
copy A（當作經理名單）：m      copy B（當作下屬名單）：e
ID | 名字    | 匯報給         ID | 名字    | 匯報給
---|--------|------         ---|--------|------
9  | Hercy  | null           9  | Hercy  | null
6  | Alice  | 9              6  | Alice  | 9
4  | Bob    | 9              4  | Bob    | 9
2  | Winston| null           2  | Winston| null

執行 JOIN：, 條件：m.employee_id（經理ID） = e.reports_to（下屬的匯報對象）
從copy A取 Hercy (ID=9)
到copy B找：誰的「匯報給」=9？
- Alice: 匯報給=9 ✓ → 連接：Hercy(經理) + Alice(下屬)
- Bob: 匯報給=9 ✓ → 連接：Hercy(經理) + Bob(下屬)

連接結果：
m.employee_id | m.name | m.reports_to | m.age | e.employee_id | e.name | e.reports_to | e.age
--------------|--------|--------------|-------|---------------|--------|--------------|------
9             | Hercy  | null         | 43    | 6             | Alice  | 9            | 41
9             | Hercy  | null         | 43    | 4             | Bob    | 9            | 36
```
