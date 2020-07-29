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


```js
insertNodes(
    editor: Editor,
    nodes: Node | Node[],
    options: {
      at?: Location
      match?: (node: Node) => boolean
      mode?: 'highest' | 'lowest'
      hanging?: boolean
      select?: boolean
      voids?: boolean
    } = {}
  ) {
    Editor.withoutNormalizing(editor, () => {
      const { hanging = false, voids = false, mode = 'lowest' } = options
      let { at, match, select } = options

      if (Node.isNode(nodes)) {
        nodes = [nodes]
      }

      if (nodes.length === 0) {
        return
      }

      const [node] = nodes

      // By default, use the selection as the target location. But if there is
      // no selection, insert at the end of the document since that is such a
      // common use case when inserting from a non-selected state.
      if (!at) {
        if (editor.selection) {
          at = editor.selection
        } else if (editor.children.length > 0) {
          at = Editor.end(editor, [])
        } else {
          at = [0]
        }

        select = true
      }

      if (select == null) {
        select = false
      }

      if (Range.isRange(at)) {
        if (!hanging) {
          at = Editor.unhangRange(editor, at)
        }

        if (Range.isCollapsed(at)) {
          at = at.anchor
        } else {
          const [, end] = Range.edges(at)
          const pointRef = Editor.pointRef(editor, end)
          Transforms.delete(editor, { at })
          at = pointRef.unref()!
        }
      }

      if (Point.isPoint(at)) {
        if (match == null) {
          if (Text.isText(node)) {
            match = n => Text.isText(n)
          } else if (editor.isInline(node)) {
            match = n => Text.isText(n) || Editor.isInline(editor, n)
          } else {
            match = n => Editor.isBlock(editor, n)
          }
        }

        const [entry] = Editor.nodes(editor, {
          at: at.path,
          match,
          mode,
          voids,
        })

        if (entry) {
          const [, matchPath] = entry
          const pathRef = Editor.pathRef(editor, matchPath)
          const isAtEnd = Editor.isEnd(editor, at, matchPath)
          Transforms.splitNodes(editor, { at, match, mode, voids })
          const path = pathRef.unref()!
          at = isAtEnd ? Path.next(path) : path
        } else {
          return
        }
      }

      const parentPath = Path.parent(at)
      let index = at[at.length - 1]

      if (!voids && Editor.void(editor, { at: parentPath })) {
        return
      }

      for (const node of nodes) {
        const path = parentPath.concat(index)
        index++
        editor.apply({ type: 'insert_node', path, node })
      }

      if (select) {
        const point = Editor.end(editor, at)

        if (point) {
          Transforms.select(editor, point)
        }
      }
    })
},
```

---

```js
insertFragment(
    editor: Editor,
    fragment: Node[],
    options: {
      at?: Location
      hanging?: boolean
      voids?: boolean
    } = {}
  ) {
    Editor.withoutNormalizing(editor, () => {
      const { hanging = false, voids = false } = options
      let { at = editor.selection } = options

      if (!fragment.length) {
        return
      }

      if (!at) {
        return
      } else if (Range.isRange(at)) {
        if (!hanging) {
          at = Editor.unhangRange(editor, at)
        }

        if (Range.isCollapsed(at)) {
          at = at.anchor
        } else {
          const [, end] = Range.edges(at)

          if (!voids && Editor.void(editor, { at: end })) {
            return
          }

          const pointRef = Editor.pointRef(editor, end)
          Transforms.delete(editor, { at })
          at = pointRef.unref()!
        }
      } else if (Path.isPath(at)) {
        at = Editor.start(editor, at)
      }

      if (!voids && Editor.void(editor, { at })) {
        return
      }

      // If the insert point is at the edge of an inline node, move it outside
      // instead since it will need to be split otherwise.
      const inlineElementMatch = Editor.above(editor, {
        at,
        match: n => Editor.isInline(editor, n),
        mode: 'highest',
        voids,
      })

      if (inlineElementMatch) {
        const [, inlinePath] = inlineElementMatch

        if (Editor.isEnd(editor, at, inlinePath)) {
          const after = Editor.after(editor, inlinePath)!
          at = after
        } else if (Editor.isStart(editor, at, inlinePath)) {
          const before = Editor.before(editor, inlinePath)!
          at = before
        }
      }

      const blockMatch = Editor.above(editor, {
        match: n => Editor.isBlock(editor, n),
        at,
        voids,
      })!
      const [, blockPath] = blockMatch
      const isBlockStart = Editor.isStart(editor, at, blockPath)
      const isBlockEnd = Editor.isEnd(editor, at, blockPath)
      const mergeStart = !isBlockStart || (isBlockStart && isBlockEnd)
      const mergeEnd = !isBlockEnd
      const [, firstPath] = Node.first({ children: fragment }, [])
      const [, lastPath] = Node.last({ children: fragment }, [])

      const matches: NodeEntry[] = []
      const matcher = ([n, p]: NodeEntry) => {
        if (
          mergeStart &&
          Path.isAncestor(p, firstPath) &&
          Element.isElement(n) &&
          !editor.isVoid(n) &&
          !editor.isInline(n)
        ) {
          return false
        }

        if (
          mergeEnd &&
          Path.isAncestor(p, lastPath) &&
          Element.isElement(n) &&
          !editor.isVoid(n) &&
          !editor.isInline(n)
        ) {
          return false
        }

        return true
      }

      for (const entry of Node.nodes(
        { children: fragment },
        { pass: matcher }
      )) {
        if (entry[1].length > 0 && matcher(entry)) {
          matches.push(entry)
        }
      }

      const starts = []
      const middles = []
      const ends = []
      let starting = true
      let hasBlocks = false

      for (const [node] of matches) {
        if (Element.isElement(node) && !editor.isInline(node)) {
          starting = false
          hasBlocks = true
          middles.push(node)
        } else if (starting) {
          starts.push(node)
        } else {
          ends.push(node)
        }
      }

      const [inlineMatch] = Editor.nodes(editor, {
        at,
        match: n => Text.isText(n) || Editor.isInline(editor, n),
        mode: 'highest',
        voids,
      })!

      const [, inlinePath] = inlineMatch
      const isInlineStart = Editor.isStart(editor, at, inlinePath)
      const isInlineEnd = Editor.isEnd(editor, at, inlinePath)

      const middleRef = Editor.pathRef(
        editor,
        isBlockEnd ? Path.next(blockPath) : blockPath
      )

      const endRef = Editor.pathRef(
        editor,
        isInlineEnd ? Path.next(inlinePath) : inlinePath
      )

      Transforms.splitNodes(editor, {
        at,
        match: n =>
          hasBlocks
            ? Editor.isBlock(editor, n)
            : Text.isText(n) || Editor.isInline(editor, n),
        mode: hasBlocks ? 'lowest' : 'highest',
        voids,
      })

      const startRef = Editor.pathRef(
        editor,
        !isInlineStart || (isInlineStart && isInlineEnd)
          ? Path.next(inlinePath)
          : inlinePath
      )

      Transforms.insertNodes(editor, starts, {
        at: startRef.current!,
        match: n => Text.isText(n) || Editor.isInline(editor, n),
        mode: 'highest',
        voids,
      })

      Transforms.insertNodes(editor, middles, {
        at: middleRef.current!,
        match: n => Editor.isBlock(editor, n),
        mode: 'lowest',
        voids,
      })

      Transforms.insertNodes(editor, ends, {
        at: endRef.current!,
        match: n => Text.isText(n) || Editor.isInline(editor, n),
        mode: 'highest',
        voids,
      })

      if (!options.at) {
        let path

        if (ends.length > 0) {
          path = Path.previous(endRef.current!)
        } else if (middles.length > 0) {
          path = Path.previous(middleRef.current!)
        } else {
          path = Path.previous(startRef.current!)
        }

        const end = Editor.end(editor, path)
        Transforms.select(editor, end)
      }

      startRef.unref()
      middleRef.unref()
      endRef.unref()
    })
}
```