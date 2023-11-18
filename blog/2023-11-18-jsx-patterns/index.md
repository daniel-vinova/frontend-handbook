---
title: JSX patterns
slug: jsx-patterns
authors: daniel
tags: [jsx]
# hide_table_of_contents: false
---

# JSX Patterns

## 1. Presentational/Container Components

It’s common to see a React design pattern that is a combination of two components:

**Presentational Components** are concerned with **how things look**. They usually have some data and callbacks via props, and they render UI. They don't know about Redux or any data handling logic. They are often functional components.

**Container Components** are concerned with **how things work**. They provide the data and behavior to presentational components or other container components. They connect the presentational components to the Redux store.

```javascript
// Presentational Component only focus how things look
const CounterDisplay = ({ count, onIncrement, onDecrement }) => (
  <div>
    <p>Count: {count}</p>
    <button onClick={onIncrement}>Increment</button>
    <button onClick={onDecrement}>Decrement</button>
  </div>
);

// Container Component only focus how things work
const CounterContainer = () => {
  const [count, setCount] = useState(0);

  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);

  return (
    <CounterDisplay
      count={count}
      onIncrement={increment}
      onDecrement={decrement}
    />
  );
};
```

`CounterDisplay` is a presentational component that simply displays the count and the buttons to increment and decrement the count.

`CounterContainer` is a container component that manages the state and the functions to change the state, and passes them down to the `CounterDisplay` component.

**When to use**:
Use this pattern when you want to separate UI and business logic, especially in large applications where this separation can help manage complexity and reusability.

**Why use**: It helps in separation of concerns. It makes the components more reusable, understandable and testable.

**Ưu điểm**:

- Tách biệt tốt hơn các mối quan tâm.
- Tăng khả năng tái sử dụng của các component trình bày.
- Đơn giản hóa việc kiểm tra bằng cách cô lập giao diện người dùng.

**Nhược điểm**:

- Có thể dẫn đến `prop drilling` nơi bạn truyền props qua nhiều lớp.
- Có thể dẫn đến nhiều mã và do đó tạo ra nhiều độ phức tạp.
- Trong khi mô hình Container/Presentational tách biệt logic ứng dụng và rendering, React Hooks có thể đạt được điều tương tự mà không cần mô hình này hoặc viết lại một component chức năng không trạng thái thành một component lớp.
- Mô hình này có thể quá mức cho các ứng dụng nhỏ.

:::info

**Presentational Components/Container Components** are terms introduced by Dan Abramov, the creator of Redux, to describe two types of structures in React.

:::

## 2. Higher Order Components (HOC)

**Higher-Order Components (HOCs)** in React are a pattern derived from React's compositional nature. HOCs are a function that takes a component and returns a new component with additional props or behaviors.

```javascript
// A HOC that style component
function withStyles(Component) {
  return props => {
    const style = { padding: '0.2rem', margin: '1rem' }
    return <Component style={style} {...props} />
  }
}

const Button = () = <button>Click me!</button>
const Text = () => <p>Hello World!</p>

const StyledButton = withStyles(Button)
const StyledText = withStyles(Text)
```

**When to use**: Use HOCs when you want to reuse component logic. HOCs are not part of the React API, they are a pattern that emerges from React’s compositional nature.

**Why use**: HOCs allow you to reuse code, logic and bootstrap abstraction. They can render hijacking, state abstraction and manipulation, props manipulation and abstraction.

**How to use**: A HOC is a pure JavaScript function, and it could be as simple as this:

```jsx
const EnhancedComponent = withHigherOrderComponent(WrappedComponent);
```

**Ưu điểm**:

- Cho phép bạn tái sử dụng logic của component, điều này rất hữu ích cho các hoạt động tương tự trên các component khác nhau.
- HOCs có thể thêm hoặc sửa đổi props trước khi chúng đến component.
- HOCs có thể trừu tượng hóa logic phức tạp, giúp component dễ hiểu và kiểm tra hơn.

**Nhược điểm**:

- HOCs thêm props vào component được bao bọc, điều này có thể dẫn đến xung đột tên props.
- HOCs có thể thêm độ phức tạp vào mã, đặc biệt khi nhiều HOCs được sử dụng.
- Refs không được truyền qua và các phương thức tĩnh có thể bị mất khi component được bao bọc với một HOC.

:::info
HOC’s are great for **shared behavior across components that we’d rather not repeat.**
:::

## 3. Render Props

**Render Props** is a technique in React for sharing code between components using a prop whose value is a function.

```javascript
const MouseTracker = ({ render }) => {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  const handleMouseMove = (event) => {
    setPosition({
      x: event.clientX,
      y: event.clientY,
    });
  };

  return (
    <div style={{ height: "100vh" }} onMouseMove={handleMouseMove}>
      {render(position)}
    </div>
  );
};

// Usage
<MouseTracker
  render={({ x, y }) => (
    <h1>
      The mouse is at ({x}, {y})
    </h1>
  )}
/>;
```

In this example, `MouseTracker` is a functional component that uses the `useState` hook to track the mouse position. It provides the mouse position to the function passed as the `render` prop, allowing the consuming component to render whatever it wants based on the mouse position.

**When to use**: Use Render Props when you want to share functionality that isn't easy to share with traditional patterns. It's useful when you have a component that needs to be reused with different render methods.

**Why use**: It provides a way to share code between components without using state management libraries or higher-order components. It's all about component composition.

**How to use**: A component with a render prop takes a function that returns a React element and calls it instead of implementing its own render logic.

**Ưu điểm**:

- Tăng khả năng tái sử dụng mã.
- Linh hoạt hơn HOCs vì bạn có thể có nhiều Render Props trong một component duy nhất.
- Khuyến khích sự kết hợp component.

**Nhược điểm**:

- Có thể dẫn đến callback hell nếu không được quản lý đúng cách.
- Có thể khó đọc và hiểu hơn so với các mô hình truyền thống.
- Có thể dẫn đến vấn đề về hiệu suất nếu không được sử dụng đúng cách.

## 4. Children as a Function

**Children as a Function**, also known as Function as Child Component (FACC) or Child Render Props, is a technique in React where a component's children are a function. It's a form of inversion of control.

In essence, Children as a Function and Render Props are very similar patterns. The main difference is in how they are used: Children as a Function uses the `children` prop to pass the function, while Render Props can use any prop to pass the function.

Here's an example of a component with children as a function using React Hooks:

```javascript
import React, { useState } from "react";

const MouseTracker = ({ children }) => {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  const handleMouseMove = (event) => {
    setPosition({
      x: event.clientX,
      y: event.clientY,
    });
  };

  return (
    <div style={{ height: "100vh" }} onMouseMove={handleMouseMove}>
      {children(position)}
    </div>
  );
};

// Usage
<MouseTracker>
  {({ x, y }) => (
    <h1>
      The mouse is at ({x}, {y})
    </h1>
  )}
</MouseTracker>;
```

In this example, `MouseTracker` is a functional component that uses the `useState` hook to track the mouse position. It provides the mouse position to its children, which is a function. This allows the consuming component to render whatever it wants based on the mouse position.
