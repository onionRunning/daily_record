# 从react-data-grid到网格布局

## 背景

> wemore表格核心插件react-data-grid的布局是采用网格布局 本篇介绍网格布局的基本用法，以及react-data-grid 中如何使用网格布局!

## 网格布局

```md

display: grid;  // 设置网格布局当然, 也可为 inline-grid

---------------------------------------------------------

#### 划分行列属性

grid-template-columns: 100px 100px 100px; // 列宽

grid-template-rows: 100px 100px 100px; // 行高

// repeat(3, 100px) 可以替代上述效果

// repeat(auto-fill, 100px) 定列宽度, 但是容器大小不定

// grid-template-columns: 100px 1fr 2fr;  // 均匀平分 按比例划分


grid-template-columns: 8fr 2fr; // 分栏布局

grid-template-columns: repeat(12, 1fr) // 12栏

---------------------------------------------------------

#### 设置行列间距

row-gap: 10px;    // 设置行间距

column-gap: 10px;  // 设置列间距

gap: 10px 10px;  // 简写 第一个为行间距， 第二个为列间距

---------------------------------------------------------


#### 填充内容的顺序

grid-auto-flow: column;  // 列顺序

grid-auto-flow: row; // 行顺序

---------------------------------------------------------


#### 单元格内部属性

justify-items: start/ end/ center/ stretch(默认);

align-items: start/ end/ center/ stretch(默认);

place-items: <align> <justify>; // 属性集合

// 内容cell 相对于划分区域的位置 (stretch 默认撑满整个划分区域)


---------------------------------------------------------

#### 默认行列间距

grid-auto-rows: 50px;

grid-auto-columns: 50px;

---------------------------------------------------------

#### 子元素属性

// 定位列的位置 (如果超出则会拉长)

grid-column-start: 1; (列左起始)
grid-column-end: 3;   (列右终点)

// ===

grid-column: 1 3;

// 定位行的位置 

grid-row-start: 1; (行上起始)
grid-row-end: 3;  (行下终点) 

// === 

grid-row: 1 3;

// 组合可以用于定位置

```

> **这里我们基本上对网格布局有个大致的了解**

## react-data-grid 中how to use

```md
> 实现表格的思路

1. 表头单独渲染 
2. 遍历渲染行, 得到行的条数
3. 针对每一行, 采用弹性布局的方式渲染每一行的cell(cell 大小要跟着表头对应列的宽度来定)
但是这只是单纯的实现格子划分, 更加丰富的功能还需要更深入的研究!
```

### 源码解析 react-data-grid

```md

1. react组件 代码逻辑顺序(参考下)
    - default value (默认值 或 默认属性)
    - useState
    - useRef
    - computed value (function - oth hook)
    - useEffect
    - useCallback
    - event handlers (行为事件)
    - utils (可到组件外部)


2. 

step(1) - step(14)  : 如何用网格布局实现逻辑
step(15) - step(23) : 如何实现双击编辑组件渲染 enter键盘事件 重置逻辑...

    ```js

    // 编辑状态的核心逻辑, 用于判断是否渲染编辑组件 
    const [selectedPosition, setSelectedPosition] = useState<SelectCellState | EditCellState<R>>(
        initialPosition
    );
    // step:(3) rowRenderer: 自定义行
    const RowRenderer = rowRenderer ?? Row;
    ...

    // step(22)
    function handleKeyDown() {
        ...
        ...
        // 特殊键盘侠ENTER 更新选中的位置 ----> 触发 编辑态组件更新
        setSelectedPosition(({ idx, rowIdx }) => ({
            idx,
            rowIdx,
            key,
            mode: 'EDIT',
            row,
            originalRow: row
        }));
    }

    // step(23)
    function handleEditorRowChange(row: R) {

        ....
        // 重置逻辑
        setSelectedPosition((position) => ({ ...position, row }));
    }

    // 编辑态组件渲染函数
    // step:(20)
    function getSelectedCellProps(rowIdx: number) {
        if (selectedPosition.rowIdx !== rowIdx) return;
        if (selectedPosition.mode === 'EDIT') {
            return {
                ...,
                onKeyDown: handleKeyDown,
                selectedCellEditor: (<EditCell {...} handleEditorRowChange={handleEditorRowChange} />)
            }
        }
        ...
    }

    // 双击激活 selectedPosition 从而渲染编辑态组件 (绕了一大圈终于知道啦是这里是这里!)
    const selectCellLatest = useLatestFunc(
        (row: R, column: CalculatedColumn<R, SR>, enableEditor: boolean | undefined | null) => {
            const rowIdx = rows.indexOf(row);
            selectCell({ rowIdx, idx: column.idx }, enableEditor);
        }
    )

    // step:(18)
    function selectCell(position: Position, enableEditor?: boolean | null) {
        ...
        if (enableEditor && isCellEditable(position)) {
            const row = rows[position.rowIdx] as R;
            setSelectedPosition({ ...position, mode: 'EDIT', key: null, row, originalRow: row });
        }
        ... 
    }
    // step:(1)
    function getViewportRows() {
        const rowElements = [];
        ...
        for (let rowIdx = rowOverscanStartIdx; rowIdx <= rowOverscanEndIdx; rowIdx++) {
            // 分组的逻辑
            if (isGroupRow) {
                rowElements.push(...)
            } 
            ...
            // 行元素
            rowElements.push(
                // step:(2)
                <RowRenderer
                    {...props}
                    // step:(17)
                    selectCell={selectCellLatest}
                    // 传入 渲染编辑态的组件
                    // step:(19)
                    ...getSelectedCellProps(rowIdx)
                />
            )
        }
        return rowElements
    }


    // ---->  Row 继续看这个组件
    // step:(11)    
    const rowClassname = css`
        // 网格布局
        display: grid;
        // 行添加高度 (可以看到通过 设置style实现)
        grid-template-rows: var(--row-height);

        // 这里默认分的列(继续) 哈哈哈哈 继续
        // step:(12)    
        grid-template-columns: var(--template-columns);
        position: absolute;
        left: 0;
    `    
    ps. var 用于读取变量, css变量一般可用 -- 打头, 用于特殊区分, 这些变量的作用域 可在子元素上进行读取!
    // 又一个hook专门用来计算 

    // step:(13)    在最外层组件中设置
    function useCalculatedColumns() {
        ...
        let templateColumns = ''
    
        for (const column of columns) {
            // 获取美列的宽度
            let width = clampColumnWidth(unallocatedColumnWidth, column, 80);
            if (column.key !== 'add_column') { // 自定义逻辑~, 隐藏新增列类型 
                // 进行累加 实现网格布局分区的逻辑~
                templateColumns += `${width}px `;
            }
        }
        // 计算 表格需要进行分列的模块
        // step:(14)
        const layoutCssVars: Record<string, string> = { '--template-columns': templateColumns};
    }

    // step:(4)  height: 当前某行的行高
    function Row({top, className, height}) {
      
        const cells = []
        for (let index = 0; index < viewportColumns.length; index++) { 
                // 注意这里的逻辑, 编辑状态时渲染 编辑组件
                if (isCellSelected && selectedCellEditor) {
                    // step:(21)
                    cells.push(selectedCellEditor);
                } else {

                    // step:(5)
                    cells.push(<Cell  {...}  selectCell={selectCell} />)
                }
        }

        // step:(10)    
        const classNames = `${rowClassname} ${className} ....`
        return (
            <div style={{top, '--row-height': `${height}px`}} className={classNames}>
                {cells}
            </div>
        )
    } 


    // ----------> 继续看 Cell组件

    // 到了我们很熟悉的环节， 一下就是最关注的单元格！

    // formatter 这个属性是不是很熟悉, 就是我们定义column时, 单元格内部渲染的内容

    // step:(6)
    function Cell() {

        // 这个方法 用来控制 格子的位置以及格子的区间
        // gridColumnStart/gridColumnEnd 控制区间， left 用于偏移位置
        // 可以自己按照需求自行改造
        // step:(9)    
        const getCellStyle = (column, colSpan, isFirstColumn)=> {
            const temp = {
                gridColumnStart: column.idx + 1,
                gridColumnEnd: colSpan !== undefined ? `span ${colSpan}` : undefined,
                left: column.frozen ? `var(--frozen-left-${column.idx})` : undefined
            } 
            if (isFirstColumn) {
                temp.borderRight = '1px solid transparent';
            }
            return temp;
        }

        // step:(16)
        function selectCellWrapper(openEditor) {
            selectCell(row, column, openEditor);
        }

        function handleDoubleClick() {
            selectCellWrapper(true);
            onRowDoubleClick?.(row, column);
        }

        return (
            <div {...} 
                // step:(8)    
                style={getCellStyle(column, colSpan, isFirstColumn)}
                // 双击
                // step:(15)
                onDoubleClick={handleDoubleClick(isLastCell!)}
                // 右键
                onContextMenu={handleContextMenu}
            >
                <!-- formatter属性 以及属性内部的方法（笑脸） -->
                // step:(7)    
                <column.formatter
                    column={column}
                    row={row}
                    isCellSelected={isCellSelected}
                    onRowChange={onRowChange}
                />
            </div>
        )
    }

    ```
```

## 总结篇

> 到这里我们其实知道了如何用网格布局实现表格

1. 网格布局的基础属性相关
2. 关于 react-data-grid 核心逻辑, 网格分区 + 双击等操作实现编辑态

```md
1. 针对每一行, 采用网格布局，通过属性 grid-template-columns: var(--template-columns); 实现单行分区逻辑

2. 通过css方法var() 搭配 style实现

3. grid-column-start/grid-column-end， 实现格子区域的限制! (特殊单元格进行合并) 

4. 双击 + 键盘等逻辑 可以触发编辑态组件的实现(指定行列 定位格子去渲染当前编辑态组件)

5. react hook组件方法编排顺序 (这个顺序确实不错)
```

> 这里我们大致看到了一些核心的功能， react-data-grid 如何实现, 当然这个插件的功能还是比较完善的 并且自行改造的话, 也是要能够读懂其中的逻辑！

**后面会介绍虚拟列表实现以及其他的核心功能!**

## 参考文章

1. [CSS Grid 网格布局教程](https://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html)
2. [react-data-grid](https://github.com/adazzle/react-data-grid)