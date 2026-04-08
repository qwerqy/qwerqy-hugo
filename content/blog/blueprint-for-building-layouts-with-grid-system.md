---
title: "Blueprint for Building Layouts with Grid System"
date: 2023-06-08
excerpt: We will explore the importance of grid systems, discuss the perspectives of engineers and designers regarding grids, and demonstrate the process of building a React component for a page's layout using a grid system.
keywords:
  - Grid system in UI design
  - Responsive web design
  - Building Grid component in React
  - Grid vs Flex in web development
  - Designing for multiple screen sizes
  - CSS grid layout
  - Web development tutorial
  - React component tutorial
  - UI design best practices
  - Consistent UI with grid system
  - Creating layout templates in React
  - Building responsive UI with grid system
  - Styled Components in React
  - Grid system for web developers and designers
  - Introduction to grid systems in UI design
  - Responsive design with Grid and React
  - Grid component for responsive web design
  - How to use grid systems for web design.
---

In the world of UI design and development, creating responsive layouts that adapt seamlessly to various screen sizes is **crucial**. One effective tool for achieving this is a **[grid system.](https://www.interaction-design.org/literature/article/the-grid-system-building-a-solid-design-layout)** In this blog post, we will explore the importance of grid systems, discuss the perspectives of engineers and designers regarding grids, and demonstrate the process of building a React component for a page's layout using a grid system.

### Grid System

A grid system in the digital world is akin to a print layout in organizing elements on a page. It serves as a guide for designers, enabling them to create multiple layouts that support responsive themes for different screen sizes. By establishing a consistent grid structure, designers can align and position elements more efficiently, ensuring a visually appealing and harmonious user interface.

### What Grid is for web developers, and for designers

When it comes to grid systems, engineers and designers often have different perspectives. As an engineer, you might view grids as just another way of aligning contents. Perhaps you prefer using flex to achieve content alignment. On the other hand, designers perceive grid systems as essential guidelines in UI design, providing a framework for consistent layouts and proportions across different pages and sections.

At Superside, I was tasked with building two components, a standalone generic Grid component, and a Grid component that aligns with our design's grid system. We decided to call this component **Columns** to separate itself from other Grid component that was built. This Columns component will serve as **a blueprint for creating responsive layouts.** By utilizing this component, we can easily create layout templates and ensure a consistent grid structure throughout our UI. Apart from the Grid component, this component needs to be:

- **Responsive**. Able to change the columns layout at every viewport
- **Simple**. No weird sugarcoats, clear and concise approach. Little to no props setup.
- **Short**. Codes don’t go above 50 lines.

### Creating a Grid System component

For this example, I will try to recreate the **Columns** component. However, **I will leave out the responsiveness aspect** and let it be at your discretion on how you would want to implement it.

If you guys want the code, head on over to [this repo](https://github.com/qwerqy/grid-system-component).

In this guide, I will be using the following stack for bootstrapping this project:

- **Typescript**
- **Create React App**
- **Styled Components**

Start by creating a new file, let's call it `Grid.tsx`, and import the required dependencies:

```tsx title="components/grid.tsx"

```

Define the types/interfaces needed for the `Grid` component. In this case, we have `Area` and `GridProps`:

```tsx title="components/grid.tsx"
interface Area {
  name: string
  start: number[]
  end: number[]
}

interface GridProps
  extends React.PropsWithChildren<{
    gridArea?: string
    areas: Area[]
    columns?: number
    rows?: number
    backgroundColor?: string
  }> {}
```

Type `Area` will be used for the `areas` type in `GridProps` as `Area[]` . `name` is the `grid-area` CSS property for each of the element that will live inside the component. `start` and `end` takes in an array of 2 numbers, where the first number is the column, and the second number is the row.

`GridProps` will be the prop type for the `Grid` component.

Define the styled component `GridContainer` using the `styled` function from `styled-components`. This component will be responsible for rendering the grid container:

`GridContainer` will accept `GridProps` as its props for setting the value of

- `grid-template-columns`
- `grid-template-rows`
- `grid-template-areas`
- `grid-area`

```tsx title="components/grid.tsx"
const GridContainer = styled.div<GridProps>`
  display: grid;
  grid-column-gap: 12px;
  grid-template-columns: ${({ columns }) => `repeat(${columns}, 1fr)`};
  grid-template-rows: ${({ rows }) => `repeat(${rows}, 1fr)`};
  grid-template-areas: ${(props) =>
    generateGridTemplateAreas(props.areas, props.rows, props.columns)};
  grid-area: ${({ gridArea }) => gridArea};
  height: 100%;
  width: 100%;
  background-color: ${(props) => props.backgroundColor};
`
```

Optionally, for this guide, I included `background-color` just to visually separate the components that will be built.

Create a helper function `generateGridTemplateAreas` that takes in the `areas`, `rows`, and `columns` as parameters and generates the `grid-template-areas` string:

```tsx title="components/grid.tsx"
const generateGridTemplateAreas = (
  areas: Area[],
  rows?: number,
  columns?: number
): string => {
  const gridTemplateAreasArray: string[][] = []

  if (rows && columns) {
    // Initialize gridTemplateAreasArray with array of "."
    for (let i = 0; i < rows; i++) {
      gridTemplateAreasArray[i] = []
      for (let j = 0; j <= columns; j++) {
        gridTemplateAreasArray[i][j] = "."
      }
    }

    // Assign area names to corresponding grid cells
    areas.forEach((area) => {
      const { name, start, end } = area
      const [startColumn, startRow] = start
      const [endColumn, endRow] = end

      for (let row = startRow; row <= endRow; row++) {
        for (let col = startColumn; col <= endColumn; col++) {
          gridTemplateAreasArray[row][col] = name
        }
      }
    })

    // Convert gridTemplateAreasArray to a string
    return gridTemplateAreasArray.map((row) => `"${row.join(" ")}"`).join("\n")
  }
  return ""
}
```

`generateGridTemplateAreas` will be responsible in creating the value for the CSS property `grid-template-areas`. Read more about [grid-template-areas](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-template-areas) and see why it is so powerful.

That is about it to bootstrap this component. In the end, your file will look like this:

```tsx title="components/grid.tsx"

interface Area {
  name: string;
  start: number[];
  end: number[];
}

interface GridProps
  extends React.PropsWithChildren<{
    gridArea?: string;
    areas: Area[];
    columns?: number;
    rows?: number;
    backgroundColor?: string;
  }> {}

const GridContainer = styled.div<GridProps>`
  display: grid;
  grid-column-gap: 12px;
  grid-template-columns: ${({ columns }) => `repeat(${columns}, 1fr)`};
  grid-template-rows: ${({ rows }) => `repeat(${rows}, 1fr)`};
  grid-template-areas: ${(props) =>
    generateGridTemplateAreas(props.areas, props.rows, props.columns)};
  grid-area: ${({ gridArea }) => gridArea};
  height: 100%;
  width: 100%;
  background-color: ${(props) => props.backgroundColor};
`;

const generateGridTemplateAreas = (
  areas: Area[],
  rows?: number,
  columns?: number
): string => {
	const gridTemplateAreasArray: string[][] = [];

  if (rows && columns) {
    // Initialize gridTemplateAreasArray with array of "."
    for (let i = 0; i < rows; i++) {
      gridTemplateAreasArray[i] = [];
      for (let j = 0; j <= columns; j++) {
        gridTemplateAreasArray[i][j] = ".";
      }
    }

    // Assign area names to corresponding grid cells
    areas.forEach((area) => {
      const { name, start, end } = area;
      const [startColumn, startRow] = start;
      const [endColumn, endRow] = end;

      for (let row = startRow; row <= endRow; row++) {
        for (let col = startColumn; col <= endColumn; col++) {
          gridTemplateAreasArray[row][col] = name;
        }
      }
    });

    // Convert gridTemplateAreasArray to a string
    return gridTemplateAreasArray.map((row) => `"${row.join(" ")}"`).join("\n");
  }
  return "";
};

const Grid: React.FC<GridProps> = ({ children, ...props }) => {
  return (
      <GridContainer {...props}>
        {children}
      </GridContainer>
  );
```

And there, you have the basic implementation of `Grid` component.

Next, we want to be able to **automatically set the columns and rows of the component.** The idea is, **if the component is the parent**, get the columns and rows value, **else if the component is a child of a Grid component**, calculate the columns and rows of the component based on the number of columns and rows assigned to it. This way, you only need to worry about setting the columns and rows on the parent `Grid`.

Create a context object `GridContext` using `createContext` from React. This context will be used to provide values to nested `Grid` components:

```tsx title="components/grid.tsx"
const GridContext = (createContext < GridContextType) | (null > null)
```

Define the `Grid` component using the `React.FC` type. Inside the component, destructure the required props and initialize the `parentCtx` and `parentColsRows` variables using the `useContext` hook:

```tsx title="components/grid.tsx"
const Grid: React.FC<GridProps> = ({
  areas,
  columns: initialColumns = 3,
  rows: initialRows = 4,
  children,
  gridArea
}) => {
  const parentCtx = useContext(GridContext)
  const parentColsRows = parentCtx?.getColumnsAndRows(gridArea)

  // Rest of the component implementation
}
```

Create the `calculateAreaColumnsAndRows` function inside the `Grid` component. This function takes an optional `areaName` parameter and calculates the number of columns and rows for that specific area:

```tsx title="components/grid.tsx"
const calculateAreaColumnsAndRows = (
  areaName?: string
): { columns: number; rows: number } => {
  if (areaName) {
    const area = areas.find((a) => a.name === areaName)
    if (area) {
      const areaColumns = area.end[0] - area.start[0] + 1
      const areaRows = area.end[1] - area.start[1] + 1

      return {
        columns: Math.min(areaColumns),
        rows: Math.min(areaRows)
      }
    }
    return {
      columns: initialColumns,
      rows: initialRows
    }
  }
  return {
    columns: initialColumns,
    rows: initialRows
  }
}
```

_I have it implemented inside the component. If you like to be outside of the component, then you can adjust the function to accept additional params._

Define the `contextValue` object that will be passed to the `GridContext.Provider` component. It includes the `areas` and `getColumnsAndRows` function, which returns the calculated columns and rows based on the provided `gridArea`:

```tsx title="components/grid.tsx"
const contextValue: GridContextType = {
  areas,
  getColumnsAndRows: (gridArea?: string) => {
    if (gridArea) {
      return calculateAreaColumnsAndRows(gridArea)
    }

    return {
      columns: initialColumns,
      rows: initialRows
    }
  }
}
```

Finally, render the `GridContainer` component wrapped inside the `GridContext.Provider`. Pass the required props to the `GridContainer` component and render the `children`:

```tsx title="components/grid.tsx"
return (
  <GridContext.Provider value={contextValue}>
    <GridContainer
      columns={parentColsRows?.columns || initialColumns}
      rows={parentColsRows?.rows || initialRows}
      areas={areas}
      gridArea={gridArea}
    >
      {children}
    </GridContainer>
  </GridContext.Provider>
)
```

Pass the `contextValue` as a value for the provider. Values for `columns` and `rows` should come from the context of its current parent. If said parent does not exist (that component IS the parent), then pass the initial values.

Finally, export the `Grid` component.

```tsx title="components/grid.tsx"
export { Grid }
```

Once that is created, the component can be used as such:

```tsx title="app.tsx"

// This is how you will need to setup the areas
const areas = [
  { name: "header", start: [0, 0], end: [12, 0] },
  { name: "sidebar", start: [0, 1], end: [1, 10] },
  { name: "content", start: [2, 1], end: [12, 11] },
  { name: "footer", start: [0, 11], end: [12, 11] }
]

const innerAreas = [
  { name: "content1", start: [0, 0], end: [8, 11] },
  { name: "content2", start: [9, 0], end: [12, 11] }
]

const App: React.FC = () => {
  return (
    <Wrapper>
      <Canvas>
        <Grid areas={areas} columns={12} rows={12}>
          <Cell gridArea={"header"} backgroundColor={"#FFC9B5"}>
            Header
          </Cell>
          <Cell gridArea={"content"} backgroundColor={"#DDE1E4"}>
            <Grid areas={innerAreas}>
              <Cell gridArea={"content1"} backgroundColor={"#D8AA96"}>
                Content 1
              </Cell>
              <Cell gridArea={"content2"} backgroundColor={"#807182"}>
                Inner Sidebar
              </Cell>
            </Grid>
          </Cell>
          <Cell gridArea={"sidebar"} backgroundColor={"#F7B1AB"}>
            Sidebar
          </Cell>
          <Cell gridArea={"footer"} backgroundColor={"#FFC9B5"}>
            Footer
          </Cell>
        </Grid>
      </Canvas>
    </Wrapper>
  )
}

export default App

const Wrapper = styled.div`
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100vw;
  height: 100vh;
  background-color: #fff8f0;
`

const Canvas = styled.div`
  height: 600px;
  width: 800px;
  border-radius: 8px;
`

const Cell = styled.div<{
  gridArea?: string
  backgroundColor?: string
  height?: string
}>`
  background-color: ${(props) => props.backgroundColor};
  grid-area: ${(props) => props.gridArea};
  height: ${(props) => props.height};
  display: flex;
  justify-content: center;
  align-items: center;
`
```

Since I am using CRA and for the purpose of this guide, I just directly import it to `App.tsx` . I also created some styled elements to tidy things up.

One more thing to note, take a look at `Cell`:

```tsx title="app.tsx"
const Cell = styled.div<{
  gridArea?: string
  backgroundColor?: string
  height?: string
}>`
  background-color: ${(props) => props.backgroundColor};
  grid-area: ${(props) => props.gridArea};
  height: ${(props) => props.height};
  display: flex;
  justify-content: center;
  align-items: center;
`
```

For this guide, `Cell` component will be considered as **direct elements that will be bent around by `Grid` component.** You can use any other elements as you like, most importantly is to **make sure that `grid-area` CSS property is included**.

So lets spin this up in [localhost](http://localhost) and see how it looks like:

![grid-system](https://i.imgur.com/ZuNBKfR.png)

_Perfecto!_ There you have it, a working autonomous Grid component. For gap, I set it to `12px` and as you can see, even if the Grid component is nested inside, it still respects the gap property.

For that, we have managed achieved the following spec:

- **Simple**. Configurations only applied at the high level parent grid.
- **Short**. Code output is less than 50 lines. (Technically, 76 lines on the `App.tsx` but that is because I place everything else inside for this guide.)

What we did not achieve is responsiveness. I did not want to keep this guide very long, so I guess that is up to you guys to figure out. 😜

### Grid system vs Flex

Let’s take a look at the differences of using grid-based components versus flex. The key difference between the two approaches lies in their underlying mechanisms and the level of control they offer.

The Grid System component allows for a more structured and grid-based approach to layout creation. It enforces consistent alignment and spacing by defining specific grid areas and gaps. This is especially useful when you have strict grid requirements and need to maintain a cohesive grid system across your UI.

On the other hand, Flex provides a more flexible approach to layout design. It allows for fluid and dynamic arrangements of elements, primarily focusing on content alignment and distribution within a container. Flex is great for simpler layouts or when you need more adaptability in element positioning.

In summary, the Grid System component offers precise control over grid-based layouts, ensuring adherence to specific guidelines, while Flex provides more flexibility in arranging elements. Your choice between the two would depend on the complexity of your layout, the need for a strict grid structure, and the level of control you desire in your UI design.

### When to use the Grid System component

The grid system component is particularly useful in scenarios where you need to create layout templates or maintain a grid system with strict gaps. It provides a solid foundation for your UI, ensuring that elements align consistently and maintain their shape across different screen sizes. By using this component, **you can design responsive layouts with ease and precision**.

While flex can be a powerful tool for content alignment, a grid system component offers **distinct advantages** when it comes to **creating responsive layouts that adhere to specific grid guidelines**. Think of it as a **blueprint** that maintains its shape, unaffected by the placement of content blocks. By embracing a grid system, designers and developers can work together harmoniously to build visually appealing and responsive user interfaces.
