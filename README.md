---
title: React hooksを基礎から理解する (useEffect編)
tags: React hooks フロントエンド JavaScript
author: seira
slide: false
---
#React hooksとは

React 16.8 で追加された新機能です。
クラスを書かなくても、`state`などのReactの機能を、関数コンポーネントでシンプルに扱えるようになりました。

- [React hooksを基礎から理解する (useState編)](https://qiita.com/seira/items/f063e262b1d57d7e78b4)
- React hooksを基礎から理解する (useEffect編)　:point_left_tone2: 今ここ
- [React hooksを基礎から理解する (useContext編)](https://qiita.com/seira/items/fccdf4e73c59c491558d)
- [React hooksを基礎から理解する (useReducer編)](https://qiita.com/seira/items/2fbad56e84bda885c84c)
- [React hooksを基礎から理解する (useCallback編)](https://qiita.com/seira/items/8a170cc950241a8fdb23)
- [React hooksを基礎から理解する (useMemo編)](https://qiita.com/seira/items/42576765aecc9fa6b2f8)
- [React hooksを基礎から理解する (useRef編)](https://qiita.com/seira/items/0e6a2d835f1afb50544d)

## useEffectとは

`useEffect`を使うと、`useEffect`に渡された関数はレンダーの結果が画面に反映された後に動作します。
つまり`useEffect`とは、「関数の実行タイミングをReactのレンダリング後まで遅らせるhook」です。

副作用の処理（DOMの書き換え、変数代入、API通信などUI構築以外の処理）を関数コンポーネントで扱えます。
クラスコンポーネントでのライフサイクルメソッドに当たります。

- componentDidMount
- componentDidUpdate
- componentWillUnmount


参考：[React公式サイト 副作用フックの利用法](https://ja.reactjs.org/docs/hooks-effect.html)


### 副作用を実行、制御するためにuseEffectを利用する

`useEffect()`の基本構文は以下の通りです。関数コンポーネントのトップレベルで宣言します。

```JavaScript
useEffect(() => {
  /* 第1引数には実行させたい副作用関数を記述*/
  console.log('副作用関数が実行されました！')
},[依存する変数の配列]) // 第2引数には副作用関数の実行タイミングを制御する依存データを記述
```

第2引数を指定することにより、第1引数に渡された副作用関数の実行タイミングを制御することができます。Reactは第2引数の依存配列の中身の値を比較して、副作用関数をスキップするかどうかを判断します。

||説明|データ型|
|:--|:--|:--:|
|第1引数|副作用関数（戻り値はクリーンアップ関数、または何も返さない）|関数|
|第2引数|副作用関数の実行タイミングを制御する依存データが入る（省略可能）|配列|

### create-react-appでReactのコードを書く

create-react-appをひさしぶりに `npm install`しようとしたらテンプレートが出来ない😦
困っていて見つけた記事。解決出来ました。ありがとうございます。

参考：[ひさしぶりにcreate-react-appしたらテンプレートができなかった時の対処法](https://qiita.com/kijibato/items/ca74c6582141f3292240)

#### Material-UIをinstall

`Material-UI`をinstallしたら、使いたいコンポーネントをすぐ見つけられるし、勝手にスタイリングしてくれるのでテンションあがります😁

```
$ npm install @material-ui/core
```

参考：[MATERIAL-UI](https://material-ui.com/)

### クリックしたらタイトルも同時に変更されるコンポーネントを作る

<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/218656/88646415-d4fe-090c-c801-a682093244fe.gif" width=400 />


#### クラスコンポーネントで作成してみる

````react.js
import React, { Component } from 'react'
import ButtonGroup from '@material-ui/core/ButtonGroup';
import Button from '@material-ui/core/Button';

class EffectClass extends Component {
  constructor(props){
    super(props);
    this.state = {
      count: 0,
    }
  }

  componentDidMount(){
    document.title =`${this.state.count}回クリックされました`
  }

  componentDidUpdate(){
    document.title =`${this.state.count}回クリックされました`
  }

  render() {
    return (
      <>
        <p>{`${this.state.count}回クリックされました`}</p>
        <ButtonGroup color="primary" aria-label="outlined primary button group">
          <Button onClick={()=> this.setState({count: this.state.count + 1})} >
            ボタン
          </Button>
          <Button onClick={()=> this.setState({count: 0})}>
            リセット
          </Button>
        </ButtonGroup>
      </>
    )
  }
}

export default EffectClass
````

クラスコンポーネントの場合、副作用は ReactがDOMを更新したあとに起こすようにしたいので、`componentDidMount`と `componentDidUpdate`に記載します。すると`React`が`DOM`に変更を加えた後に、`document.title`を更新しています。

````react.js
  componentDidMount(){
    document.title =`${this.state.count}回クリックされました`
  }

  componentDidUpdate(){
    document.title =`${this.state.count}回クリックされました`
  }
````

#### 関数コンポーネントで作成してみる

```react.js
import React, {useState, useEffect} from 'react'
import ButtonGroup from '@material-ui/core/ButtonGroup'
import Button from '@material-ui/core/Button'

const EffectFunc = () => {
  const [count, setCount] = useState(0)
  useEffect(() => {
    document.title =`${count}回クリックされました`
  })

  return (
    <>
      <p>{`${count}回クリックされました`}</p>
      <ButtonGroup color="primary" aria-label="outlined primary button group">
        <Button onClick={()=>setCount((prev) => prev + 1)}>
          ボタン
        </Button>
        <Button onClick={()=>setCount(0)}>
          リセット
        </Button>
      </ButtonGroup>
    </>
  )
}

export default EffectFunc
```

関数コンポーネントで`useEffect`を使った場合、デフォルトでは、`useEffect`は毎回のレンダリング後に呼ばれます。第2引数を省略した場合、コンポーネントがレンダリングされるたびに、第1引数で渡した副作用関数が実行されます。

````react.js
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title =`${count}回クリックされました`
  })
````

第2引数を省略すると、コンポーネントがレンダリングされるたびに副作用関数が実行されることから、無限ループの温床になりやすいので注意する必要があります。実際には、第2引数を省略するケースはほとんどありません。


React公式サイトのstate とライフサイクルをもう一度読むと理解が進みました。
参考：[React公式サイト state とライフサイクル](https://ja.reactjs.org/docs/state-and-lifecycle.html)

#### 初回レンダリング時のみ副作用関数を実行させる

副作用関数を初回レンダリング時の一度だけ実行させたい場合、第2引数に空の依存配列`[]`を指定します。
この場合、初回レンダリング時のみ副作用関数が実行され、`document.title`は更新されません。

````react.js
  useEffect(() => {
    document.title =`${count}回クリックされました`
    console.log(`再レンダーされました`)
  },[])
````

<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/218656/393cb3ea-75c9-84f7-0954-9de09290719d.gif" width=500 />

#### 依存配列の値が変化した場合のみ副作用関数を実行させる

`useEffect（）`の第２引数に[count]を渡すと、`count`に変化があったときだけ副作用関数を実行します。

```react.js
import React, {useState, useEffect} from 'react'
import { makeStyles } from '@material-ui/core/styles';
import ButtonGroup from '@material-ui/core/ButtonGroup'
import Button from '@material-ui/core/Button'
import Input from '@material-ui/core/Input';

const useStyles = makeStyles((theme) => ({
  root: {
    '& > *': {
      margin: theme.spacing(1),
    },
  },
}));

const EffectFunc = () => {
  const classes = useStyles();
  const [count, setCount] = useState(0)
  const [name, setName] = useState({
    lastName: '',
    firstName: ''
  })
  useEffect(() => {
    document.title =`${count}回クリックされました`
  },[count])

  return (
    <>
      <p>{`${count}回クリックされました`}</p>
      <ButtonGroup color="primary" aria-label="outlined primary button group">
        <Button onClick={()=>setCount((prev) => prev + 1)}>
          ボタン
        </Button>
        <Button onClick={()=>setCount(0)}>
          リセット
        </Button>
      </ButtonGroup>
      <p>{`私の名前は${name.lastName} ${name.firstName}です`}</p>
      <form className={classes.root} noValidate autoComplete="off">
        <Input
          placeholder="姓"
          value={name.lastName}
          onChange={(e)=>{setName({...name,lastName: e.target.value})}}/>
        <Input
          placeholder="名"
          value={name.firstName}
          onChange={(e)=>{setName({...name,firstName: e.target.value})}}/>
      </form>
    </>
  )
}

export default EffectFunc
```

`useEffect`の第2引数に`[count]`を取るとき

```react.js
  useEffect(() => {
    document.title =`${count}回クリックされました`
    console.log(`再レンダーされました`)
  },[count])
```

<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/218656/b28eda79-56c1-7630-2a27-aa1724f967c7.gif" width=500 />

`name`が更新されても副作用関数は実行されず、`count`が更新された場合だけ、`document.title`が実行され、再描画されていることがわかります。


#### クリーンアップについて

クリーンアップとはイベントリスナの削除、タイマーのキャンセルなどのことです。
`クリーンアップ関数`をreturnすると、2度目以降のレンダリング時に前回の副作用を消してしまうことができます。

##### クラスコンポーネントの場合

componentWillUnmountは、クリーンアップ（addEventLitenerの削除、タイマーのキャンセルなど）に使用されます。componentDidMountに副作用を追加し、componentWillUnmountで副作用を削除します。

```react.js
componentDidMount() {
  elm.addEventListener('click', () => {})
}

componentWillUnmount() {
  elm.removeEventListener('click', () => {})
}
```


##### 関数コンポーネントの場合

上記に相当するhookは以下。「クリーンアップ関数」をreturnすることで、2度目以降のレンダリング時に前回の副作用を消してしまうことができます。

```react.js
useEffect(() => {
   elm.addEventListener('click', () => {})

  // returned function will be called on component unmount 
  return () => {
     elm.removeEventListener('click', () => {})
  }
}, [])
```

##### ライフサイクル

`useEffect()`では、副作用関数がクリーンアップ関数を返すことで、マウント時に実行した処理をアンマウント時に解除します。またその副作用関数は、毎回のレンダリング時に実行され、新しい副作用関数を実行する前に、ひとつ前の副作用処理をクリーンアップします。

このようにマウント処理とアンマウント処理の繰り返し処理のことを「ライフサイクル」と言います。

##最後に

次回は `useContext` について書きたいと思います。


###参考にさせていただいたサイト
https://reactjs.org/
