# 🧩 Dynamo Node Connection Guide
## Place Sections On Sheets Grid — Step-by-Step for Beginners

> **Good news first:** The file `Place_Sections_On_Sheets_Grid.dyn` already has all connections built in.
> Open it in Dynamo and you should see wires connecting every node automatically.
>
> **This guide** is for you if the wires are missing, or if you want to understand exactly
> how each node connects so you can rebuild it yourself.

---

## 📖 PART 1 — How Dynamo Nodes Work (Read This First!)

Every Dynamo node looks like a box. Here is what the parts mean:

```
        ┌──────────────────────────────────┐
        │        Node Name                 │
        ├──────────────────────────────────┤
 ●──────│◀ input port 1                    │
 ●──────│◀ input port 2                    │──────● OUTPUT
        │                                  │
        └──────────────────────────────────┘
         ↑ LEFT SIDE                ↑ RIGHT SIDE
         Data comes IN here         Data goes OUT here
```

### 🔑 The Golden Rule
- **RIGHT side dot** = OUTPUT (drag FROM here)
- **LEFT side dot**  = INPUT  (drag TO here)

### 🖱️ How to Draw a Wire
1. Hover over the **right-side dot** of the first node
2. Your mouse cursor changes to a crosshair ✛
3. **Click and hold** the left mouse button
4. **Drag** your mouse to the **left-side dot** of the next node
5. **Release** the mouse button
6. A coloured line (wire) appears — you're done! 🎉

### ❌ How to Delete a Wire
- Right-click the wire and choose **Delete**

---

## 📐 PART 2 — Node Layout (Birds-Eye View)

Below is a map of where every node sits in the Dynamo canvas.
Numbers in `[brackets]` are the connection steps explained in Part 3.

```
Y=0    [1]──────────────[2]──────────────[3]──────────────────────[13]
       Sheets           Get Sheets       All Sheets               Selected
       Category         Category                                   Sheet
                                            │ [4]                    ▲
                                            │                        │[13]
Y=50                                        │              Get First Sheet
                                            │              ▲
                                            │              │[3] (from All Sheets)
                                            │              │
Y=150  [5]──────────────[6]──────────────[7]─────[8]       │
       Sections         Get Sections     All      Get       │
       Category         Category         Section  Section   │
                                         Views    Names     │
                                            │[8]    │[9]    │
                                            │       ▼       │
Y=200                              [11]◀──[10]──▶String.   │        [16]
                                   Filter         Contains  │         ▼
                                   Sections          ▲[10]  │    Viewport
                                   by Name     Filter       │    Positions
                                     │[12]     Keyword      │         ▲
Y=250                                │         [10]         │         │
                                     ▼                      │         │
Y=200                          [12]─────────▶ Calculate ───────────[16]
                                              Positions
                                              ▲  ▲  ▲
                              [4](sheets)─────┘  │  │
                              [12](sections)──────┘  │
                              [15](config)───────────┘
                                              ▲
Y=400                   [14]──▶ Pass Config ──┘[15]
                         ▲
                    Grid Configuration [14]
```

---

## 🔢 PART 3 — Step-by-Step Connections (All 16 Wires)

Make these connections **in order**. Each step = one wire.

---

### 🟦 GROUP A — Get All Sheets (Steps 1–4)

These nodes find every sheet in your Revit project.

---

#### ✅ Step 1 of 16
**Connect: `Sheets Category` → `Get Sheets Category`**

```
 ┌──────────────────┐              ┌────────────────────────┐
 │  Sheets Category │              │  Get Sheets Category   │
 │                  │              │                        │
 │   "Sheets"  ●────┼──────────────┼▶ Category Name         │
 └──────────────────┘              └────────────────────────┘
              ↑                                 ↑
     RIGHT dot (output)              LEFT dot (input port 0)
     DRAG FROM HERE                  DROP HERE
```

- **From:** Right dot of `Sheets Category` (the only dot on the right)
- **To:** Left dot of `Get Sheets Category` labeled `Category Name`

---

#### ✅ Step 2 of 16
**Connect: `Get Sheets Category` → `All Sheets`**

```
 ┌────────────────────────┐        ┌──────────────────┐
 │  Get Sheets Category   │        │   All Sheets     │
 │                        │        │                  │
 │              Category ●┼────────┼▶ category        │
 └────────────────────────┘        └──────────────────┘
                       ↑                    ↑
              RIGHT dot "Category"   LEFT dot "category"
              DRAG FROM HERE         DROP HERE
```

- **From:** Right dot `Category` of `Get Sheets Category`
- **To:** Left dot `category` of `All Sheets`

---

#### ✅ Step 3 of 16
**Connect: `All Sheets` → `Get First Sheet`**

```
 ┌──────────────────┐        ┌──────────────────┐
 │   All Sheets     │        │  Get First Sheet  │
 │                  │        │                  │
 │       Elements ●─┼────────┼▶ sheets          │
 └──────────────────┘        └──────────────────┘
```

- **From:** Right dot `Elements` of `All Sheets`
- **To:** Left dot `sheets` of `Get First Sheet`

---

#### ✅ Step 4 of 16
**Connect: `All Sheets` → `Calculate Positions` (sheets input)**

> ⚠️ This node has **3 input ports** on the left side. You are connecting to the **MIDDLE** one labeled `sheets` (second from top = index 1).

```
 ┌──────────────────┐        ┌────────────────────────┐
 │   All Sheets     │        │   Calculate Positions  │
 │                  │        ├────────────────────────┤
 │       Elements ●─┼──────┐ │◀ sections  (port 0)    │
 └──────────────────┘      └▶│◀ sheets    (port 1) ◀──┤ ← DROP HERE
                              │◀ config    (port 2)    │
                              └────────────────────────┘
```

- **From:** Right dot `Elements` of `All Sheets`
- **To:** Left dot `sheets` of `Calculate Positions` **(port 1 — the MIDDLE input)**

---

### 🟩 GROUP B — Get All Section Views (Steps 5–8)

These nodes find all sections in your Revit project.

---

#### ✅ Step 5 of 16
**Connect: `Sections Category` → `Get Sections Category`**

```
 ┌──────────────────────┐        ┌──────────────────────────┐
 │  Sections Category   │        │  Get Sections Category   │
 │                      │        │                          │
 │  "Section Views" ●───┼────────┼▶ Category Name           │
 └──────────────────────┘        └──────────────────────────┘
```

- **From:** Right dot of `Sections Category`
- **To:** Left dot `Category Name` of `Get Sections Category`

---

#### ✅ Step 6 of 16
**Connect: `Get Sections Category` → `All Section Views`**

```
 ┌──────────────────────────┐        ┌──────────────────────┐
 │  Get Sections Category   │        │   All Section Views  │
 │                          │        │                      │
 │                Category ●┼────────┼▶ category            │
 └──────────────────────────┘        └──────────────────────┘
```

- **From:** Right dot `Category` of `Get Sections Category`
- **To:** Left dot `category` of `All Section Views`

---

#### ✅ Step 7 of 16
**Connect: `All Section Views` → `Get Section Names`**

```
 ┌──────────────────────┐        ┌──────────────────────┐
 │   All Section Views  │        │   Get Section Names  │
 │                      │        │                      │
 │           Elements ●─┼────────┼▶ sections            │
 └──────────────────────┘        └──────────────────────┘
```

- **From:** Right dot `Elements` of `All Section Views`
- **To:** Left dot `sections` of `Get Section Names`

---

#### ✅ Step 8 of 16
**Connect: `All Section Views` → `Filter Sections by Name` (list input)**

> ⚠️ `Filter Sections by Name` has 2 input ports. Connect to the **TOP** one (`list`).

```
 ┌──────────────────────┐        ┌──────────────────────────────┐
 │   All Section Views  │        │   Filter Sections by Name    │
 │                      │        ├──────────────────────────────┤
 │           Elements ●─┼────────┼▶ list   (port 0)  ← DROP HERE│
 └──────────────────────┘        │◀ mask   (port 1)             │
                                  └──────────────────────────────┘
```

- **From:** Right dot `Elements` of `All Section Views`
- **To:** Left dot `list` of `Filter Sections by Name` **(port 0 — the TOP input)**

---

### 🟨 GROUP C — Filter Sections by Name (Steps 9–11)

These nodes check which sections have "Wall" in their name.

---

#### ✅ Step 9 of 16
**Connect: `Get Section Names` → `String.Contains` (str input)**

> ⚠️ `String.Contains` has 3 input ports. Connect to the **TOP** one (`str`).

```
 ┌──────────────────────┐        ┌──────────────────────────┐
 │   Get Section Names  │        │      String.Contains     │
 │                      │        ├──────────────────────────┤
 │                    ●─┼────────┼▶ str        (port 0) ← DROP│
 └──────────────────────┘        │◀ searchFor  (port 1)     │
                                  │◀ ignoreCase (port 2)     │
                                  └──────────────────────────┘
```

- **From:** Right dot of `Get Section Names` (only one output)
- **To:** Left dot `str` of `String.Contains` **(port 0 — the TOP input)**

---

#### ✅ Step 10 of 16
**Connect: `Filter Keyword` → `String.Contains` (searchFor input)**

> ⚠️ Connect to the **MIDDLE** input (`searchFor`), not the top one.

```
 ┌──────────────────────┐        ┌──────────────────────────┐
 │    Filter Keyword    │        │      String.Contains     │
 │                      │        ├──────────────────────────┤
 │       "Wall"       ●─┼──┐     │◀ str        (port 0)     │
 └──────────────────────┘  └─────┼▶ searchFor  (port 1) ← DROP│
                                  │◀ ignoreCase (port 2)     │
                                  └──────────────────────────┘
```

- **From:** Right dot of `Filter Keyword`
- **To:** Left dot `searchFor` of `String.Contains` **(port 1 — the MIDDLE input)**

---

#### ✅ Step 11 of 16
**Connect: `String.Contains` → `Filter Sections by Name` (mask input)**

> ⚠️ Connect to the **BOTTOM** input of `Filter Sections by Name` (`mask`).

```
 ┌──────────────────────────┐        ┌──────────────────────────────┐
 │      String.Contains     │        │   Filter Sections by Name    │
 │                          │        ├──────────────────────────────┤
 │                    bool ●┼──┐     │◀ list   (port 0)             │
 └──────────────────────────┘  └─────┼▶ mask   (port 1) ← DROP HERE │
                                      └──────────────────────────────┘
```

- **From:** Right dot `bool` of `String.Contains`
- **To:** Left dot `mask` of `Filter Sections by Name` **(port 1 — the BOTTOM input)**

---

### 🟥 GROUP D — Calculate Grid Positions (Steps 12–16)

These nodes calculate where each section goes on the sheet.

---

#### ✅ Step 12 of 16
**Connect: `Filter Sections by Name` → `Calculate Positions` (sections input)**

> ⚠️ `Filter Sections by Name` has 2 output ports. Use the **TOP** one (`in` = sections that matched).

```
 ┌──────────────────────────────┐        ┌────────────────────────┐
 │   Filter Sections by Name    │        │   Calculate Positions  │
 │                              │        ├────────────────────────┤
 │                      in (●)──┼────────┼▶ sections  (port 0) ◀──┤ ← DROP
 │                     out  ●   │        │◀ sheets    (port 1)    │
 └──────────────────────────────┘        │◀ config    (port 2)    │
          ↑                              └────────────────────────┘
   TOP output "in"
   DRAG FROM HERE
```

- **From:** Right dot `in` of `Filter Sections by Name` **(TOP output)**
- **To:** Left dot `sections` of `Calculate Positions` **(port 0 — the TOP input)**

---

#### ✅ Step 13 of 16
**Connect: `Get First Sheet` → `Selected Sheet`**

```
 ┌──────────────────┐        ┌──────────────────┐
 │  Get First Sheet │        │   Selected Sheet │
 │                  │        │  (Watch Node)    │
 │                ●─┼────────┼▶ x               │
 └──────────────────┘        └──────────────────┘
```

- **From:** Right dot of `Get First Sheet`
- **To:** Left dot `x` of `Selected Sheet`

---

#### ✅ Step 14 of 16
**Connect: `Grid Configuration` → `Pass Config`**

```
 ┌───────────────────────┐        ┌─────────────────┐
 │   Grid Configuration  │        │   Pass Config   │
 │                       │        │                 │
 │  {startX,startY,...}●─┼────────┼▶ config         │
 └───────────────────────┘        └─────────────────┘
```

- **From:** Right dot of `Grid Configuration`
- **To:** Left dot `config` of `Pass Config`

---

#### ✅ Step 15 of 16
**Connect: `Pass Config` → `Calculate Positions` (config input)**

> ⚠️ Connect to the **BOTTOM** input of `Calculate Positions` (`config` = port 2).

```
 ┌─────────────────┐        ┌────────────────────────┐
 │   Pass Config   │        │   Calculate Positions  │
 │                 │        ├────────────────────────┤
 │               ●─┼──┐     │◀ sections  (port 0)    │
 └─────────────────┘  │     │◀ sheets    (port 1)    │
                       └─────┼▶ config    (port 2) ◀──┤ ← DROP HERE
                              └────────────────────────┘
```

- **From:** Right dot of `Pass Config`
- **To:** Left dot `config` of `Calculate Positions` **(port 2 — the BOTTOM input)**

---

#### ✅ Step 16 of 16
**Connect: `Calculate Positions` → `Viewport Positions`**

```
 ┌────────────────────────┐        ┌──────────────────────┐
 │   Calculate Positions  │        │   Viewport Positions │
 │                        │        │   (Watch Node)       │
 │                      ●─┼────────┼▶ x                   │
 └────────────────────────┘        └──────────────────────┘
```

- **From:** Right dot of `Calculate Positions`
- **To:** Left dot `x` of `Viewport Positions`

---

## ✅ PART 4 — Complete Connection Checklist

Use this to tick off each connection as you make it:

| ✓ | # | FROM Node | FROM Port | TO Node | TO Port |
|---|---|-----------|-----------|---------|---------|
| ☐ | 1 | Sheets Category | (only output) | Get Sheets Category | Category Name |
| ☐ | 2 | Get Sheets Category | Category | All Sheets | category |
| ☐ | 3 | All Sheets | Elements | Get First Sheet | sheets |
| ☐ | 4 | All Sheets | Elements | Calculate Positions | **sheets (port 1)** |
| ☐ | 5 | Sections Category | (only output) | Get Sections Category | Category Name |
| ☐ | 6 | Get Sections Category | Category | All Section Views | category |
| ☐ | 7 | All Section Views | Elements | Get Section Names | sections |
| ☐ | 8 | All Section Views | Elements | Filter Sections by Name | **list (port 0)** |
| ☐ | 9 | Get Section Names | (only output) | String.Contains | **str (port 0)** |
| ☐ | 10 | Filter Keyword | (only output) | String.Contains | **searchFor (port 1)** |
| ☐ | 11 | String.Contains | bool | Filter Sections by Name | **mask (port 1)** |
| ☐ | 12 | Filter Sections by Name | **in (port 0)** | Calculate Positions | **sections (port 0)** |
| ☐ | 13 | Get First Sheet | (only output) | Selected Sheet | x |
| ☐ | 14 | Grid Configuration | (only output) | Pass Config | config |
| ☐ | 15 | Pass Config | (only output) | Calculate Positions | **config (port 2)** |
| ☐ | 16 | Calculate Positions | (only output) | Viewport Positions | x |

---

## 🗺️ PART 5 — Full Wiring Diagram

```
  ┌──────────────────┐   ①   ┌──────────────────────┐   ②   ┌──────────────┐
  │ Sheets Category  ├───────▶  Get Sheets Category  ├───────▶  All Sheets  │
  └──────────────────┘       └──────────────────────┘       └──────┬───────┘
                                                                    │ ③ ④
                                                          ③  ┌──────▼───────────┐   ⑬   ┌───────────────┐
                                                             │  Get First Sheet ├───────▶  Selected Sheet│
                                                             └──────────────────┘       │   (Watch) 👁️  │
                                                                                        └───────────────┘

  ┌──────────────────┐   ⑤   ┌──────────────────────┐   ⑥   ┌────────────────────┐
  │Sections Category ├───────▶ Get Sections Category ├───────▶  All Section Views │
  └──────────────────┘       └──────────────────────┘       └──────┬─────────────┘
                                                                    │ ⑦ ⑧
                                                          ⑦ ┌───────▼──────────┐
                                                             │ Get Section Names│
                                                             └───────┬──────────┘
                                                                     │ ⑨
  ┌──────────────────┐   ⑩   ┌──────────────────────┐               │
  │  Filter Keyword  ├───────▶                       ◀───────────────┘
  │     "Wall"       │       │    String.Contains    │
  └──────────────────┘       └───────────┬───────────┘
                                         │ ⑪
                              ⑧ ┌────────▼─────────────────┐
                          ┌────▶│  Filter Sections by Name  │
                          │    └────────────┬───────────────┘
                          │                 │ ⑫ (in port)
  ┌─────────────────────┐ │    ④ ┌──────────▼───────────────────────────────────────┐
  │  Grid Configuration ├─┤      │              Calculate Positions                 │◀─── ④
  └──────────┬──────────┘ │      │   (sections ▲) (sheets ▲) (config ▲)            │
             │ ⑭          │      └──────────────────────────────────┬───────────────┘
             ▼            └ ⑧ →                                     │ ⑯
      ┌─────────────┐    ⑮→config input                ┌────────────▼───────────────┐
      │  Pass Config├──────────────────────────────────▶  Viewport Positions         │
      └─────────────┘                                  │     (Watch) 👁️             │
                                                        └────────────────────────────┘
```

---

## 🔧 PART 6 — Troubleshooting

| Problem | What to do |
|---------|-----------|
| **Wire won't connect** | Make sure you drag FROM the RIGHT side (output) TO the LEFT side (input). You cannot connect two outputs together. |
| **Node shows red/orange** | All 3 inputs of that node need to be connected. Check the checklist above. |
| **Wire disappears when I release** | You dropped it in the wrong place. Try again — hover until you see the circle glow before releasing. |
| **Can't find a node** | Use the Search bar (Ctrl+F) or look in the left Library panel. |
| **I connected to the wrong port** | Right-click the wrong wire → Delete, then reconnect to the correct port. |
| **Nothing happens when I click Run** | Check that no nodes are showing red errors. Fix any errors first. |
| **Script runs but no sections appear** | Make sure sections in your Revit project have "Wall" in their name, or change the `Filter Keyword` node from `"Wall"` to match your section names. |

---

## ⚙️ PART 7 — Configure the Script

Once connected, you can customise these two nodes:

### 📍 Grid Configuration node
Edit these values to control where sections are placed:
```
startX  = 1.0   ← How far from left edge of sheet (in feet)
startY  = 8.0   ← How far from top edge of sheet (in feet)
spacingX = 2.8  ← Gap between columns of viewports (feet)
spacingY = 3.8  ← Gap between rows of viewports (feet)
maxCols  = 2    ← How many sections per row (before starting a new row)
```

### 🔍 Filter Keyword node
Change `"Wall"` to whatever word appears in your section view names:
```
"Wall"    ← finds sections named "Wall Section 1", "Typical Wall Detail", etc.
"Column"  ← finds sections named "Column Detail", "Column Section A", etc.
"Section" ← finds ALL sections
```

---

## 🏁 Final Result

When all 16 connections are made, your canvas should look like this — with coloured lines (wires) connecting every node:

```
[Sheets Category]──────[Get Sheets Category]──────[All Sheets]──────[Get First Sheet]──────[Selected Sheet 👁️]
                                                       │
[Sections Category]────[Get Sections Category]──[All Section Views]──[Get Section Names]
                                                       │                    │
                                              [Filter Sections]◀──────[String.Contains]◀──[Filter Keyword]
                                                       │
[Grid Configuration]──[Pass Config]──────────▶[Calculate Positions]◀──────(from All Sheets)
                                                       │
                                               [Viewport Positions 👁️]
```

You're done! Click the **Run** button at the bottom of Dynamo to execute the script. 🚀
