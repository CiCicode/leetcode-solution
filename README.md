# leetcode-solution
My LeetCode solutions with detailed explanations

類別 1：基礎 WHERE 條件過濾

### 1757. Recyclable and Low Fat Products
**連結**: [https://leetcode.com/problems/recyclable-and-low-fat-products/description/]
#### SQL 解法

```sql
SELECT product_id
FROM Products
WHERE low_fats = 'Y' AND recyclable = 'Y';
```
學習重點:
基本 WHERE 條件
AND 運算符使用

### 584. Find Customer Referee
**連結**: [https://leetcode.com/problems/find-customer-referee/description/]


### 1934. Confirmation Rate 計算每個用戶的電子郵件確認率
**連結**: [LeetCode 1934](https://leetcode.com/problems/confirmation-rate/)
#### SQL 解法

```sql

#### 解題思路
1. 使用 LEFT JOIN保留所有註冊用戶
2. 條件聚合計算 confirmed 次數
3. 處理除以0的情況（IFNULL）
4. 四捨五入到小數點後2位

#### 學習重點
- 條件聚合：`SUM(CASE WHEN ... THEN 1 ELSE 0 END)`
- 安全除法：`IFNULL(分子/分母, 0)`
- COUNT() 的 NULL 處理：`COUNT(c.user_id)` vs `COUNT(*)`
- ROUND() 函數應用

#### SQL 解法

```sql
SELECT 
    s.user_id,
    ROUND(
        IFNULL(
            SUM(CASE WHEN c.action = 'confirmed' THEN 1 ELSE 0 END) / COUNT(c.user_id),
            0
        ),
        2
    ) AS confirmation_rate
FROM Signups s
LEFT JOIN Confirmations c ON s.user_id = c.user_id
GROUP BY s.user_id;
