---
title: Business logic in React application
slug: business-logic-in-react-application
authors: daniel
tags: [business-logic, custom-hook]
# hide_table_of_contents: false
---

# Business logic in React application

"Business logic" là thuật ngữ chỉ các quy tắc, quy định và quy trình mà hệ thống của bạn cần tuân theo để hoạt động đúng như mong đợi. Trong lập trình, business logic thường được thực hiện trong các hàm hoặc phương thức của đối tượng.

<!--truncate-->

Dưới đây là một ví dụ về business logic trong một React component:

```jsx
import React, { useState } from "react";

function ExampleComponent() {
  const [count, setCount] = useState(0);

  function increment() {
    // Business logic: chỉ cho phép tăng count nếu nó nhỏ hơn 10
    if (count < 10) {
      setCount(count + 1);
    }
  }

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={increment}>Click me</button>
    </div>
  );
}

export default ExampleComponent;
```

Và sau đây là cách tách business logic ra thành một custom hook:

```jsx
import { useState } from "react";

// Custom hook
function useCount() {
  const [count, setCount] = useState(0);

  function increment() {
    if (count < 10) {
      setCount(count + 1);
    }
  }

  return { count, increment };
}

// Component sử dụng custom hook
function ExampleComponent() {
  const { count, increment } = useCount();

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={increment}>Click me</button>
    </div>
  );
}

export default ExampleComponent;
```

Trong ví dụ trên, chúng ta đã tạo một custom hook `useCount` để chứa business logic. Component `ExampleComponent` sau đó sử dụng hook này để lấy `count` và `increment`.

### Should we always split business logic to a custom hook

Không phải lúc nào chúng ta cũng cần tách business logic ra thành custom hooks. Việc này phụ thuộc vào nhiều yếu tố như độ phức tạp của logic, mức độ tái sử dụng và yêu cầu về bảo dưỡng mã nguồn.

Nếu logic đơn giản và không cần tái sử dụng, việc tạo một custom hook có thể làm tăng độ phức tạp không cần thiết. Tuy nhiên, nếu logic phức tạp và được sử dụng ở nhiều nơi trong ứng dụng, việc tạo một custom hook có thể giúp mã nguồn dễ đọc, dễ bảo dưỡng hơn và giúp tái sử dụng code hiệu quả hơn.

Có một số trường hợp cụ thể mà việc sử dụng custom hooks sẽ rất hữu ích:

1. **Khi logic được sử dụng ở nhiều nơi trong ứng dụng:** Việc tạo một custom hook giúp tái sử dụng code, giảm bớt sự lặp lại và giúp mã nguồn dễ bảo dưỡng hơn.

```jsx
import { useState } from "react";

// Custom hook
function useCounter() {
  const [count, setCount] = useState(0);

  function increment() {
    setCount(count + 1);
  }

  return { count, increment };
}

// Component 1
function Component1() {
  const { count, increment } = useCounter();

  return (
    <div>
      <button onClick={increment}>Component 1 count: {count}</button>
    </div>
  );
}

// Component 2
function Component2() {
  const { count, increment } = useCounter();

  return (
    <div>
      <button onClick={increment}>Component 2 count: {count}</button>
    </div>
  );
}
```

2. **Khi logic phức tạp:** Việc tách logic ra khỏi component giúp giữ cho component dễ đọc và dễ hiểu hơn, đồng thời giúp tập trung vào việc hiển thị UI.

```jsx
import { useState, useEffect } from "react";

// Custom hook
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch(url)
      .then((response) => response.json())
      .then((data) => {
        setData(data);
        setLoading(false);
      })
      .catch((error) => {
        setError(error);
        setLoading(false);
      });
  }, [url]);

  return { data, loading, error };
}

// Component
function Component() {
  const { data, loading, error } = useFetch("https://api.example.com/data");

  if (loading) return "Loading...";
  if (error) return "Error!";

  return <div>{/* Render data here */}</div>;
}
```

3. **Khi muốn chia sẻ logic giữa các ứng dụng:** Custom hooks có thể được đóng gói và chia sẻ qua các dự án khác nhau, giúp tăng tính tái sử dụng và giảm thời gian phát triển.

### What are some best practices for organizing business logic in React applications?

Dưới đây là các best pratices khi tổ chức business logic trong ứng dụng React:

1. **Phân tách Mối quan tâm:** Giữ logic UI riêng biệt khỏi business logic. Điều này có thể được thực hiện bằng cách sử dụng custom hooks để đóng gói business logic.

```jsx
// Custom hook
function useBusinessLogic() {
  // Business logic goes here
}

// Component
function MyComponent() {
  const data = useBusinessLogic();

  // UI logic goes here
}
```

2. **Logic tái sử dụng với Custom Hooks:** Nếu một phần business logic được sử dụng ở nhiều component, hãy xem xét việc di chuyển nó vào một custom hook. Điều này thúc đẩy việc tái sử dụng code và giữ cho các component của bạn sạch sẽ và tập trung vào việc render.

```jsx
// Custom hook
function useBusinessLogic() {
  // Business logic goes here
}

// Component
function MyComponent() {
  const data = useBusinessLogic();

  // UI logic goes here
}
```

3. **Colocation:** Giữ code liên quan cùng nhau. Nếu một phần business logic chỉ được sử dụng ở một component, thì thường dễ hiểu hơn nếu nó được đặt gần nơi nó được sử dụng.

```jsx
// Component
function MyComponent() {
  // Business logic that's only used in this component
  const [count, setCount] = useState(0);

  function increment() {
    setCount(count + 1);
  }

  // UI logic
  // ...
}
```

4. **Testability:** Bằng cách tách business logic ra khỏi các component, bạn có thể viết các bài kiểm tra cho business logic mà không phụ thuộc vào UI. Điều này làm cho các bài kiểm tra của bạn đơn giản và đáng tin cậy hơn.

```jsx
// Custom hook
function useCounter() {
  const [count, setCount] = useState(0);

  const increment = () => {
    setCount(count + 1);
  };

  return { count, increment };
}
```

```jsx
// Test
it("tests the counter hook", () => {
  const { result } = renderHook(() => useCounter());

  // Initial state
  expect(result.current.count).toBe(0);

  // Increment
  act(() => {
    result.current.increment();
  });

  // Assert new state
  expect(result.current.count).toBe(1);
});
```

5. **Sử dụng Context cho Shared State:** Nếu nhiều component cần truy cập hoặc sửa đổi cùng một state, hãy xem xét việc sử dụng Context API. Điều này có thể giúp bạn tránh prop drilling.

```jsx
// Context
const MyContext = React.createContext();

// Provider in a high-level component
<MyContext.Provider value={/* shared state */}>
  {/* children */}
</MyContext.Provider>

// Access in a child component
const value = useContext(MyContext);
```

6. **Sử dụng Reducers cho Complex State Logic:** Nếu component của bạn có logic state phức tạp, hãy xem xét việc sử dụng hook `useReducer`. Điều này cho phép bạn xử lý các chuyển đổi state phức tạp theo cách dự đoán được.

```jsx
import React, { useReducer } from "react";

// Action types
const INCREMENT = "INCREMENT";
const DECREMENT = "DECREMENT";

// Reducer
function counterReducer(state, action) {
  switch (action.type) {
    case INCREMENT:
      return { count: state.count + 1 };
    case DECREMENT:
      return { count: state.count - 1 };
    default:
      return state;
  }
}

// Component
function Counter() {
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });

  return (
    <div>
      Count: {state.count}
      <button onClick={() => dispatch({ type: INCREMENT })}>+</button>
      <button onClick={() => dispatch({ type: DECREMENT })}>-</button>
    </div>
  );
}

export default Counter;
```

7. **Code Splitting:** Nếu ứng dụng của bạn có các phần business logic lớn mà không luôn cần thiết, hãy xem xét việc chia code. Điều này có thể giúp giảm thời gian tải ban đầu của ứng dụng của bạn.

```jsx
// Using React.lazy to split code
const MyComponent = React.lazy(() => import("./MyComponent"));

// In a component that renders MyComponent
<Suspense fallback={<div>Loading...</div>}>
  <MyComponent />
</Suspense>;
```

Đây chỉ là các hướng dẫn, không phải là quy tắc cứng nhắc. Cách tổ chức tốt nhất cho business logic của bạn sẽ phụ thuộc vào các chi tiết cụ thể của ứng dụng của bạn.
