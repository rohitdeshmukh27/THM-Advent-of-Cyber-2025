# 🎄 TryHackMe – Race Conditions: Toy to The World

# Task 1: Introduction

## 📖 The Story

The snow falls heavily over Wareville as chaos erupts at TBFC headquarters. What should be the busiest shopping day of the season has turned into a nightmare.

**"How did everyone get a confirmation email?!"** shouts a frustrated customer service representative, staring at her screen. **"We only had ten SleighToys in stock!"**

The Best Festival Company (TBFC) has launched its limited-edition SleighToy, with only ten pieces available at midnight. Within seconds, thousands rushed to buy one, but something strange happened. More than ten lucky customers received confirmation emails stating that their orders were successful. Confusion spread fast. How could everyone have bought the "last" toy?

You're called into the operations centre, where screens flicker with order confirmations. Everything looks normal on the surface—customers are happy, payments are processed, and all systems are operational. But the inventory system tells a different story.

The inventory manager pulls up the stock dashboard. "Look at this", she says, pointing at the screen. "We started with 10 units. But we've confirmed 25 orders. The stock count is now showing **-15**. How is that even possible?"

Then, on one of the monitoring dashboards, a pattern emerges. Multiple checkout requests hit the server at the **exact same microsecond**, all checking stock before any of them could update it.

```
🐰 Timestamp: 00:00:00.001 - Stock check: 10 units ✓
🐰 Timestamp: 00:00:00.001 - Stock check: 10 units ✓
🐰 Timestamp: 00:00:00.001 - Stock check: 10 units ✓
[...15 more simultaneous requests...]
🐰 All requests: "Stock available, processing order..."
```

McSkidy is called in to investigate. She quickly noticed that multiple buyers purchased at the **exact moment**, slipping through the system's timing flaw. Sir Carrotbane's mischievous **Bandit Bunnies** had found a way to exploit this chaos, flooding the checkout with rapid clicks using automated tools.

By morning, TBFC faced:

- Angry shoppers who thought they'd secured a toy
- Inventory showing impossible negative stock
- A mystery that revealed just how dangerous a few milliseconds could be during the holiday rush

**Your mission:** Investigate how the race condition exploit works, replicate the attack in a controlled environment, and understand how to prevent this vulnerability from ruining Christmas shopping.

---

## 🎯 Learning Objectives

- Understand what **race conditions** are and how they can affect web applications
- Learn how to **identify and exploit** race conditions in web requests
- Discover how concurrent requests can **manipulate stock or transaction values**
- Master the use of **Burp Suite** to send parallel requests for race condition testing
- Explore practical **mitigation techniques** to prevent race condition vulnerabilities
- Learn the difference between **TOCTOU**, **shared resource**, and **atomicity violation** race conditions

---

# Task 2: Understanding Race Conditions

## 🏁 What is a Race Condition?

A **race condition** happens when two or more actions occur at the same time, and the system's outcome depends on the **order** or **timing** in which they finish. In web applications, this often happens when multiple users or automated requests simultaneously access or modify shared resources, such as inventory or account balances.

If proper synchronisation isn't in place, this can lead to unexpected results.

### Common Consequences

| Issue                         | Description                                               |
| ----------------------------- | --------------------------------------------------------- |
| **Duplicate transactions**    | Same purchase processed multiple times                    |
| **Oversold items**            | More items sold than exist in inventory                   |
| **Unauthorized data changes** | Data modified by concurrent requests in unexpected ways   |
| **Negative inventory**        | Stock counts go below zero due to simultaneous deductions |
| **Account manipulation**      | Balance transfers or withdrawals processed out of order   |

---

## 📋 Types of Race Conditions

Race condition attacks can generally be divided into three categories:

### 1. Time-of-Check to Time-of-Use (TOCTOU)

A **TOCTOU** race condition happens when a program checks something first and uses it later, but the data changes in between. This means what was true at the time of the check might no longer be true when the action happens.

**Real-World Analogy:**
It's like checking if a toy is in stock, and by the time you click "Buy", someone else has already bought it—but the system doesn't realize this and processes your order anyway.

**Example:**

```
Thread 1: Check stock → 10 units available ✓
Thread 2: Check stock → 10 units available ✓
Thread 1: Purchase item → Stock becomes 9
Thread 2: Purchase item → Stock becomes 8 (should have failed)
```

Two users buy the same "last item" at the same time because the stock was checked before it was updated.

---

### 2. Shared Resource

This occurs when multiple users or systems try to change the same data simultaneously without proper control. Since both updates happen together, the final result depends on which one finishes last, creating confusion.

**Real-World Analogy:**
Think of two cashiers updating the same inventory spreadsheet at once. One overwrites the other's work, resulting in incorrect stock counts.

**Example:**

```
Initial stock: 10
User A: Read stock (10) → Calculate new stock (9) → Write (9)
User B: Read stock (10) → Calculate new stock (9) → Write (9)
Final stock: 9 (should be 8)
```

---

### 3. Atomicity Violation

An **atomic operation** should happen all at once, either fully done or not at all. When parts of a process run separately, another request can sneak in between and cause inconsistent results.

**Real-World Analogy:**
It's like paying for an item, but before the system confirms it, someone else changes the price or availability.

**Example:**

```
User A: Deduct payment from account
[User B interrupts: Cancel order]
User A: Confirm order (but payment already deducted)
Result: User charged but order canceled
```

A payment is recorded, but the order confirmation fails because another request interrupts the process.

---

## 🎯 Why Race Conditions Are Dangerous

### In E-Commerce Systems

- **Financial Loss** — Selling more items than in stock leads to fulfillment costs
- **Customer Dissatisfaction** — Canceled orders after payment confirmation
- **Reputation Damage** — "How did you sell 25 items when only 10 existed?"

### In Banking Systems

- **Duplicate Withdrawals** — Same withdrawal processed multiple times
- **Negative Balances** — Accounts go below zero
- **Lost Transactions** — Transfers that disappear

### In Real-World Attacks

- **2016** — Multiple e-commerce sites experienced race condition exploits during Black Friday sales
- **2019** — Banking app allowed users to withdraw money multiple times by exploiting timing gaps
- **2023** — Cryptocurrency exchange lost millions due to concurrent transaction processing flaws

**Sir Carrotbane's Bandit Bunnies** have weaponized these same tactics to sabotage Christmas shopping by flooding TBFC's checkout system with parallel requests.

---

# Task 3: Practical — Exploiting the Race Condition

## 🛠️ Time for Some Action

Now that we understand the basic concepts, let's take the example of the TBFC shopping cart app and exploit the race condition using **Burp Suite**.

---

## ⚙️ Step 1: Configuring the Environment

### Configure Firefox to Use Burp Suite

First, we will configure Firefox to route traffic through Burp Suite.

1. On the AttackBox, open **Firefox**
2. Click the **FoxyProxy icon** in the toolbar
3. Select the **Burp** profile

This configuration ensures all browser requests are sent to Burp Suite for interception and manipulation.

---

### Launch Burp Suite

1. Click on the **Burp Suite** icon on the Desktop to launch the application
2. You will see an introductory screen—choose **Temporary project in memory**
3. Click **Next**
4. On the configuration screen, click **Start Burp** to start the application

Once the application starts, you will see the main Burp Suite interface, where we will use the **Proxy** and **Repeater** tabs to exploit the race condition.

---

### Disable Intercept Mode

⚠️ **Before proceeding**, ensure that you turn off "Intercept" in Burp Suite:

1. Open the **Proxy** tab
2. Check the **Intercept** sub-tab
3. If the button says **"Intercept on"**, click it so it changes to **"Intercept off"**

This step ensures that Burp Suite no longer holds your browser requests and allows them to pass through normally while still logging them in the HTTP history.

---

## 🛒 Step 2: Making a Legitimate Request

Now that the environment is configured, let's make a normal purchase request.

### Login to TBFC Web Application

1. Open Firefox and visit the webapp at: `http://MACHINE_IP`
2. You will see the TBFC login page

**Credentials:**

- **Username:** `attacker`
- **Password:** `attacker@123`

---

### Add Item to Cart

After logging in, you'll arrive at the main dashboard, which shows the limited-edition **SleighToy** with only **10 units** available.

1. Click **Add to Cart** for the SleighToy
2. Click **Checkout** to go to the checkout page

---

### Complete the Purchase

On the checkout page:

1. Click **Confirm & Pay** to complete the purchase
2. You should see a success message confirming the order

✅ **Excellent!** You've made a legitimate purchase. Now let's see how attackers can exploit the race condition.

---

## 🚀 Step 3: Exploiting the Race Condition

### Capture the Checkout Request

1. Navigate back to **Burp Suite**
2. Click on **Proxy > HTTP history**
3. Find the **POST** request to the `/process_checkout` endpoint
4. Right-click that entry and choose **Send to Repeater**

This action copies the exact HTTP request (headers, cookies, body) into Burp's Repeater tool.

---

### Create a Request Group

1. Switch to the **Repeater** tab and confirm the request appears there
2. Right-click on the first tab
3. Select **Add tab to group**
4. Click on **Create tab group**
5. Enter a name for the tab group, such as **cart**
6. Click **Create**

You now have a tab group that will hold multiple copies of the same request.

---

### Duplicate the Request

1. Right-click the request tab
2. Select **Duplicate tab**
3. When prompted, enter the number of copies you want (for example, **15**)
4. You'll now have that many identical request tabs inside the **cart** group

---

### Send Parallel Requests

⚠️ **This is the critical step** — sending all requests simultaneously to trigger the race condition:

1. Use the **Repeater toolbar Send dropdown menu**
2. Select **Send group in parallel (last-byte sync)**

This option launches all copies at once and waits for the final byte from each response, maximizing the timing overlap to trigger race conditions.

3. Click **Send group (parallel)**

This will launch all 15 requests to the server **simultaneously**. The server will attempt to handle them at the same time, which will cause the timing bug to appear (due to multiple orders being processed concurrently).

---

## 🎉 Step 4: Verify the Exploit

### Check the Results

1. Navigate back to the web application at `http://MACHINE_IP`
2. Check the inventory page or your order history

**What You'll See:**

- Multiple confirmed orders (more than the 10 available)
- SleighToy stock reduced (possibly going **negative**, e.g., **-15**)
- The first flag displayed on the screen

---

### What Just Happened?

Here's the timeline of the exploit:

```
Initial Stock: 10 units

Request 1: Check stock → 10 available → Process order
Request 2: Check stock → 10 available → Process order
Request 3: Check stock → 10 available → Process order
[...15 total requests, all checking simultaneously...]

All requests pass the stock check before any can update the database.

Final Result: 15 orders confirmed, stock = 10 - 15 = -5
```

Each request saw the **same initial stock value** because they all checked it before any of them could update it. This is a classic **TOCTOU (Time-of-Check to Time-of-Use)** race condition.

---

## 🔒 Mitigation Techniques

### Understanding the Vulnerability

The attacker logged in and made a normal purchase of the limited SleighToy. Using Burp Suite, they captured the checkout request and sent it multiple times in parallel. Because the app didn't handle simultaneous checkouts correctly, each request succeeded before the stock could update. This allowed the attacker to buy more toys than available, resulting in a race condition and pushing the stock into negative values.

---

### How to Prevent This

Here are several mitigation measures to avoid this vulnerability:

| Technique                        | Description                                                                                |
| -------------------------------- | ------------------------------------------------------------------------------------------ |
| **Atomic Database Transactions** | Use database transactions so stock deduction and order creation execute as a single unit   |
| **Final Stock Validation**       | Perform a final stock check right before committing the transaction to prevent overselling |
| **Idempotency Keys**             | Implement unique keys for checkout requests to ensure duplicates aren't processed          |
| **Rate Limiting**                | Block rapid, repeated checkout attempts from the same user or session                      |
| **Pessimistic Locking**          | Lock the inventory row during the transaction so other requests must wait                  |
| **Optimistic Locking**           | Use version numbers or timestamps to detect concurrent modifications                       |

---

### Example: Atomic Transaction

**Before (Vulnerable):**

```python
stock = get_stock(item_id)  # Check
if stock > 0:
    create_order(user, item_id)  # Use (gap here!)
    update_stock(item_id, stock - 1)
```

**After (Protected):**

```python
with database.transaction():
    stock = get_stock(item_id, lock=True)  # Lock the row
    if stock > 0:
        create_order(user, item_id)
        update_stock(item_id, stock - 1)
    else:
        raise OutOfStockError()
    # Commit or rollback atomically
```

---

### Example: Idempotency Key

**Client sends:**

```http
POST /process_checkout HTTP/1.1
X-Idempotency-Key: abc123-unique-uuid

{"item": "SleighToy", "quantity": 1}
```

**Server logic:**

```python
key = request.headers.get('X-Idempotency-Key')
if already_processed(key):
    return previous_response(key)  # Return cached response

# Process request normally
result = process_checkout(request.data)
cache_response(key, result)
return result
```

This ensures that even if the same request is sent 15 times, only the first one is processed. The rest receive the cached response.

---

## 🏆 Repeat the Exploit

**Your mission:** Repeat the same steps for the **Bunny Plush (Blue)** item.

1. Add **Bunny Plush (Blue)** to cart
2. Proceed to checkout
3. Capture the request in Burp Suite
4. Duplicate and send parallel requests
5. Check the inventory for negative stock and retrieve the second flag

---

## ❓ Questions

**What is the flag value once the stocks are negative for SleighToy Limited Edition?**

```
THM{WINNER_OF_R@CE007}
```

**Repeat the same steps as were done for ordering the SleighToy Limited Edition. What is the flag value once the stocks are negative for Bunny Plush (Blue)?**

```
THM{WINNER_OF_Bunny_R@ce}
```

---
