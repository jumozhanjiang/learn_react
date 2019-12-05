### 子组件接收父组件的参数
- 子组件
  ```js
  import React,{useContext} from 'react';
  import './compTest02.css';
  import PropTypes from 'prop-types';

  function CompTest02(props) {
      let {count} = props;
      return (
          <>
              <div>{count}</div>
          </>
      );
  }
  export default CompTest02;
  CompTest02.propTypes = {
      count: PropTypes.number
  };
  ```
- 父组件
  ```js
  import React,{useState} from 'react';
  import CompTest02 from "../compTest02/compTest02";
  import './compTest01.css';
  function CompTest01() {
      let [count,setCount] = useState(0);
      return (
          <>
              <CompTest02 count={count}/>
              <button onClick={() => setCount(count + 1)}>{count}</button>
          </>
      );
  }
  export default CompTest01;
  ```

### 父组件获取子组件的值
- 子组件
  ```js
  import React,{useState,useImperativeHandle} from 'react';
  import './compTest03.css';

  function CompTest03(props) {
      let [test01,setTest01] = useState(0);
      let {cRef} = props;
      // 里面的函数或变量都是对外暴露的
      useImperativeHandle(cRef, () => ({
          // 把getTest01方法暴露出去，返回子组件的test01的值
          getTest01: () => {
              return test01;
          }
      }));
      return (
          <div >
              {/*点击按钮改变子组件的内部值**/}
              <button onClick={() => {setTest01(test01 + 1)}}>改变子组件的内部变量{test01}</button>
          </div>
      );
  }
  export default CompTest03;
  ```
- 父组件
  ```js
  import React,{useState,createContext,useRef} from 'react';
  import CompTest03 from "../compTest03/compTest03";
  import './compTest01.css';
  function CompTest01() {
      // 获取父组件的ref
      const testRef = useRef();
      // 开始获取内部值
      const getChildTest01 = () => {
          // 执行子组件的getTest01的函数取出内部值
          console.log(testRef.current.getTest01());
      };
      return (
          <>
            {/*把父组件的ref传入子组件*/}
            <CompTest03 cRef={testRef}/>
            {/**点击按钮准备获取子组件的内部值**/}
            <button onClick={getChildTest01}>获取子组件的值</button>
          </>
      );
  }
  export default CompTest01;
  ```
### 子组件获取父组件的参数或方法
> 不建议使用,子组件里面要引入父组件 这样组件之间的耦合性太强了， 建议采用父组件获取子组件变量的方式让父组件自己给自己重新赋值
- 子组件
  ```js
  import React,{useContext} from 'react';
  import './compTest02.css';
  import PropTypes from 'prop-types';
  // 把父组件的上下文参数给引入
  import {NumberContext} from "../compTest01/compTest01";

  function CompTest02(props) {
      let {count} = props;
      // 使用useContext的方法 获取父组件的setCount的函数
      let {setCount} = useContext(NumberContext);
      return (
          <>
              {/**点击按钮调用父组件的setCount函数**/}
              <button onClick={() => setCount(count +1 )}>{count}</button>
          </>
      );
  }
  export default CompTest02;
  CompTest02.propTypes = {
      count: PropTypes.number
  };
  ```
- 父组件
  ```js
  import React,{useState,createContext,useRef} from 'react';
  import CompTest02 from "../compTest02/compTest02";
  import './compTest01.css';
  // 获取组件的上下文
  export const NumberContext = createContext();
  function CompTest01() {
      let [count,setCount] = useState(0);
      return (
          <>
            {/**把该组件的count值与setCount函数通过上下文的形式传递给子组件**/}
            <NumberContext.Provider value={{count,setCount}}>
                <CompTest02 count={count}/>
            </NumberContext.Provider>
          </>
      );
  }
  export default CompTest01;
  ```
