# A Note on `React.createElement()`'s return value.

```typescript
interface IReactElementTreeNode {
  /** internal symbol */
  $$typeof: Symbol(react.element);

  key: null | string;
  props: { 
    children: IReactElementTreeNode[] | string;
  };
  ref: null | string | (ref: HTMLElement) => any;

  /** the React Component of this node */
  type: string | React.ComponentType | Symbol(react.fragment);
  
  _owner: FiberNode;
  
}
```
