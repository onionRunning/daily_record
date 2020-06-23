### 富文本编辑器

### slate 源码阅读篇

### 源码结构

```
- slate
- slate-history           撤销记录
- slate-hyperscript
- slate-react             与react相关的组件
  -slate.tsx              Slate组件  
```


组件:

```js
<Slate value={value} onChange={v => setValue(v)} editor={editor}>
    <Toolbar>
        // 编辑器顶部操作
    </Toolbar>
    <Editable
        renderElement={renderElement}
        renderLeaf={renderLeaf}
        placeholder={'please edit'}
        onKeyDown = {
            () => {

            }
        }
    />

</Slate>


const editor = useMemo(() => withHistory(withReact(createEditor())), [])

createEditor: 编辑器对象集合,里面有很多方法

withReact:    向编辑器添加特定于React和DOM的行为,(apply, setFragmentData, insertData, onChange)

withHistory:  历史撤销等操作 暂时忽略...

```

slate.tsx: Slate 组件

```js
const Slate = () => {
    const { editor, children, onChange, value, ...rest } = props
    const [key, setKey] = useState(0)

    // add 拓展  editor, 当输入 value 发生change时触发
    const context: [ReactEditor] = useMemo(() => {
        editor.children = value
        Object.assign(editor, rest)
        return [editor]
    }, [key, value, ...Object.values(rest)])

    // onChange 触发
    const onContextChange = useCallback(() => {
        onChange(editor.children)
        setKey(key + 1)
    }, [key, onChange])

    // 编辑器 与编辑器对应的onChange事件设置 key -value 映射 储存，其他地方进行触发: EDITOR_TO_ON_CHANGE 这个对象
    EDITOR_TO_ON_CHANGE.set(editor, onContextChange)
    // 组件销毁时触发清空: EDITOR_TO_ON_CHANGE
    useEffect(() => {
        return () => {
            EDITOR_TO_ON_CHANGE.set(editor, () => {})
        }
    }, [])
    return (
        <SlateContext.Provider value={context}>
            <EditorContext.Provider value={editor}>
                <FocusedContext.Provider value={ReactEditor.isFocused(editor)}>
                    {children}
                </FocusedContext.Provider>
            </EditorContext.Provider>
        </SlateContext.Provider>
    )
}

```