```tsx
/**
 * 在本例中 alertNumber、 confirmNumberClick、 modalNumberConfirm 等函数都更新了 number 的 state, 导致页面重新渲染。
 * 但是，在这几个函数中都包含有一个 setTimeout 的闭包函数。虽然页面更新后 number 的 state 指向了新的地址，但是，在闭包函数中 number 的 state 仍然指向原来的值。
 * 另外，更新状态的 setState 为异步函数，当更新状态后立刻 log 该状态的值时，并不能得到最新值。
 */
import React, { useState } from 'react'
import { Modal, Button } from 'antd'
import { ExclamationCircleOutlined } from '@ant-design/icons'

const { confirm } = Modal
let renderIndex = 1

const Hook: React.FC<{}> = () => {
  const [number, setNumber] = useState(1)
  const [modalVisible, setModalVisible] = useState<boolean>(false)

  const changeNumber = () => {
    setNumber(number + 1)
  }

  const alertNumber = () => {
    setNumber(number + 5)
    setTimeout(() => {
      console.log('值展示 number :>>', number)
    }, 1000)
  }

  const confirmNumberClick = () => {
    setNumber(number + 5)
    confirm({
      title: `确认`,
      icon: <ExclamationCircleOutlined />,
      onOk() {
        return new Promise((resolve) => {
          console.log('闭包函数inside :>>', number)
          resolve()
        })
      },
    })
  }
  const modalNumberClick = () => {
    setModalVisible(true)
  }

  const modalNumberConfirm = () => {
    setNumber(number + 5)
    setTimeout(() => {
      console.log('number :>>', number)
      setModalVisible(false)
    }, 1000)
  }

  const render = () => {
    return (
      <div
        style={{
          display: 'flex',
          justifyContent: 'space-between',
          width: '500px',
          marginTop: '20px',
        }}
      >
        <Button type="primary" onClick={changeNumber}>
          值递增
        </Button>
        <Button type="default" onClick={alertNumber}>
          值展示
        </Button>
        <Button type="link" onClick={confirmNumberClick}>
          值确认
        </Button>
        <Button type="dashed" onClick={modalNumberClick}>
          值弹出
        </Button>
      </div>
    )
  }

  console.log(
    // eslint-disable-next-line no-plusplus
    `第${renderIndex++}次渲染 :>>`,
    'number :>>',
    number,
    'modalVisible :>>',
    modalVisible
  )

  return (
    <>
      <p style={{ height: 20 }}>最新值： {number}</p>
      {render()}

      <Modal
        title="确认"
        footer={null}
        visible={modalVisible}
        onCancel={() => setModalVisible(false)}
      >
        <div>{number}</div>

        <Button type="primary" onClick={modalNumberConfirm}>
          确认
        </Button>
      </Modal>
    </>
  )
}

export default Hook
```
