#透過類型驅動設計 (Type-Driven Design) 進行領域建模

這是一個旨在引導 AI 使用類型驅動設計（Type-Driven Design, TDD）和代數數據類型（Algebraic Data Types, ADT）來進行領域建模的 Prompt 範本。其核心目標是「讓非法狀態無法被表示出來」（Make illegal states unrepresentable），從而創建出更健壯、更易於理解和維護的程式碼。

--- 

## Prompt 結構

### 1. **角色設定 (Role Setting)**

> 你是一位精通領域驅動設計（DDD）和函數式編程的軟體架構師。你的核心設計哲學是「讓非法狀態無法被表示出來」。你擅長使用代數數據類型（ADT），特別是積類型（Product Types）和和類型（Sum Types），來精確地對業務領域進行建模，並將業務規則編碼到類型系統中。

### 2. **任務描述 (Task Description)**

> 你的任務是為以下業務場景設計一組類型定義。這些類型定義必須：
> 1.  **精確反映業務規則**：嚴格遵循業務描述，不允許任何業務規則之外的狀態存在。
> 2.  **消除非法狀態**：利用 ADT（和類型與積類型）來確保任何不合法的狀態組合在類型層面就是不可能的。
> 3.  **清晰表達意圖**：類型命名應清晰、無歧義，直接反映其所代表的業務概念。
> 4.  **提供範例**：為每個核心類型提供 1-2 個實例化範例，以展示其如何被使用。
> 5.  **語言選擇**：請使用 [在此處指定語言，例如：TypeScript, Rust, Haskell, F#] 來編寫類型定義。

### 3. **業務場景 (Business Scenario)**

> **[在此處詳細描述你的業務場景]**
> 
> *   **核心實體**：描述場景中的核心名詞或對象（例如：訂單、用戶、文章）。
> *   **狀態與流程**：描述這些實體的不同狀態以及它們之間如何流轉（例如：訂單可以是「待支付」、「已支付」、「已發貨」、「已完成」、「已取消」）。
> *   **業務規則與約束**：詳細說明不同狀態下的約束條件（例如：只有「已支付」的訂單才能被「發貨」；「已取消」的訂單不能再進行任何操作；只有「已驗證」的用戶才能發表評論）。

### 4. **輸出格式 (Output Format)**

> 請按照以下格式進行輸出：
> 
> 1.  **核心類型定義**：列出所有設計的類型、介面或枚舉。
> 2.  **設計思路闡述**：簡要解釋你的設計思路，特別是說明你如何使用積類型和和類型來消除非法狀態。
> 3.  **範例代碼**：提供清晰的範例，展示如何創建和使用這些類型的實例。

--- 

## 範例應用

以下是如何使用這個 Prompt 範本來解決一個具體問題的例子。

### **填充後的 Prompt**

**角色設定**

你是一位精通領域驅動設計（DDD）和函數式編程的軟體架構師。你的核心設計哲學是「讓非法狀態無法被表示出來」。你擅長使用代數數據類型（ADT），特別是積類型（Product Types）和和類型（Sum Types），來精確地對業務領域進行建模，並將業務規則編碼到類型系統中。

**任務描述**

你的任務是為以下業務場景設計一組類型定義。這些類型定義必須：
1.  精確反映業務規則。
2.  消除非法狀態。
3.  清晰表達意圖。
4.  提供範例。
5.  請使用 **TypeScript** 來編寫類型定義。

**業務場景**

我們需要為一個簡單的電子商務系統設計一個「訂單」（Order）模型。

*   **核心實體**：訂單 (Order)
*   **狀態與流程**：
    1.  **待支付 (PendingPayment)**：訂單已創建，等待用戶支付。此狀態下，訂單有一個唯一的支付鏈接。
    2.  **已支付 (Paid)**：用戶已成功支付。此狀態下，訂單有一個交易 ID 和支付時間。
    3.  **已發貨 (Shipped)**：訂單已從倉庫發貨。此狀態下，訂單需要包含物流公司和運單號。
    4.  **已完成 (Completed)**：用戶已確認收貨。此狀態下，訂單有一個完成時間。
    5.  **已取消 (Cancelled)**：訂單已被取消。此狀態下，訂單需要有一個取消原因。
*   **業務規則與約束**：
    *   一個訂單在同一時間只能處於一個狀態。
    *   不同狀態下，訂單擁有的資訊是不同的（例如，「待支付」狀態沒有運單號，「已發貨」狀態沒有支付鏈接）。
    *   必須嚴格區分這些狀態，不允許出現「既是已支付又是已取消」或「待支付狀態下有運單號」這樣的情況。

**輸出格式**

請按照「核心類型定義」、「設計思路闡述」和「範例代碼」的格式進行輸出。

### **預期的輸出 (Expected Output)**

#### 核心類型定義

```typescript
// 使用「積類型」定義每個狀態下獨有的數據結構
type PendingPaymentState = {
  type: 'PendingPayment';
  paymentUrl: string;
  createdAt: Date;
};

type PaidState = {
  type: 'Paid';
  transactionId: string;
  paidAt: Date;
};

type ShippedState = {
  type: 'Shipped';
  shippingCompany: string;
  trackingNumber: string;
};

type CompletedState = {
  type: 'Completed';
  completedAt: Date;
};

type CancelledState = {
  type: 'Cancelled';
  cancellationReason: string;
};

// 使用「和類型」將所有互斥的狀態組合起來
type OrderState = 
  | PendingPaymentState 
  | PaidState 
  | ShippedState 
  | CompletedState 
  | CancelledState;

// 定義訂單的核心數據，這些數據在所有狀態下都存在
type Order = {
  id: string;
  customerId: string;
  items: OrderItem[];
  state: OrderState; // 狀態是所有可能狀態中的一種
};

type OrderItem = {
  productId: string;
  quantity: number;
  price: number;
};
```

#### 設計思路闡述

本次設計的核心是利用 TypeScript 的「可辨識聯合類型」（Discriminated Unions），這正是 ADT 中「和類型」的一種實現。我將訂單的每個狀態（`PendingPayment`, `Paid`, `Shipped` 等）都定義為一個獨立的類型（積類型），每個類型都包含一個 `type` 屬性作為辨識符，以及該狀態下獨有的數據。

然後，我使用 `|` 操作符將這些獨立的狀態類型組合成一個大的 `OrderState` 類型（和類型）。這樣做的好處是：

1.  **狀態互斥**：一個 `Order` 的 `state` 屬性在同一時間只能是 `OrderState` 中的一種，從根本上杜絕了狀態的混淆。
2.  **數據隔離**：與特定狀態相關的數據（如 `paymentUrl`, `trackingNumber`）被封裝在各自的狀態類型中。這就使得「待支付狀態下擁有運單號」這種非法狀態在類型層面就無法被表示出來。如果我們嘗試在一個 `PendingPaymentState` 的對象上訪問 `trackingNumber`，TypeScript 編譯器會立即報錯。
3.  **類型安全**：在使用訂單狀態時，我們可以通過檢查 `state.type` 屬性來進行類型收窄（Type Narrowing），從而安全地訪問特定狀態下的數據，而無需進行不安全的類型斷言。

#### 範例代碼

```typescript
// 範例1：創建一個「待支付」的訂單
const pendingOrder: Order = {
  id: 'order-123',
  customerId: 'customer-abc',
  items: [{ productId: 'prod-001', quantity: 2, price: 100 }],
  state: {
    type: 'PendingPayment',
    paymentUrl: 'https://example.com/pay/order-123',
    createdAt: new Date(),
  },
};

// 範例2：處理一個訂單
function processOrder(order: Order): void {
  switch (order.state.type) {
    case 'PendingPayment':
      console.log(`訂單待支付，支付鏈接為: ${order.state.paymentUrl}`);
      // 在這裡，你無法訪問 order.state.trackingNumber，編譯器會報錯
      break;
    case 'Shipped':
      console.log(`訂單已發貨，物流公司: ${order.state.shippingCompany}, 運單號: ${order.state.trackingNumber}`);
      break;
    case 'Cancelled':
      console.log(`訂單已取消，原因: ${order.state.cancellationReason}`);
      break;
    // ... 處理其他狀態
    default:
      const _exhaustiveCheck: never = order.state;
      return _exhaustiveCheck;
  }
}

processOrder(pendingOrder);
```

--- 

這個 Prompt 範本可以幫助您系統性地思考如何將業務規則轉化為類型，並生成高質量的、類型安全的領域模型。

