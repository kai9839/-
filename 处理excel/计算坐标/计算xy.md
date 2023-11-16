计算出两个点之间的x和y坐标的差值，即：

 Δx = (x₂ - x₁) / (n + 1) Δy = (y₂ - y₁) / (n + 1)

然后，根据差值逐步插值计算每个点的坐标。假设插值的第i个点为Pᵢ，其坐标为： 

Pᵢ(x₁ + i * Δx, y₁ + i * Δy)

 Δx = 2.099

```
const XLSX = require('xlsx');

// 读取Excel文件
const workbook = XLSX.readFile('tree_data.xlsx');
const worksheet = workbook.Sheets[workbook.SheetNames[0]];

// 获取已知的树的位置
const treeStart = {
  x: parseFloat(worksheet['A1'].v),
  y: parseFloat(worksheet['B1'].v)
};

const treeEnd = {
  x: parseFloat(worksheet['A2'].v),
  y: parseFloat(worksheet['B2'].v)
};

// 计算未知的树的数量
let treeCount = 0;
for (let i = 3; ; i++) {
  const xCell = 'A' + i;
  const yCell = 'B' + i;

  if (!worksheet[xCell] || !worksheet[yCell]) {
    break;
  }

  if (!worksheet[xCell].v && !worksheet[yCell].v) {
    treeCount++;
  }
}

// 假设树沿着水平方向均匀分布
const deltaX = (treeEnd.x - treeStart.x) / (treeCount + 1);

// 计算每棵树的坐标
const trees = [];
let treeIndex = 0;
for (let i = 3; ; i++) {
  const xCell = 'A' + i;
  const yCell = 'B' + i;

  if (!worksheet[xCell] || !worksheet[yCell]) {
    break;
  }

  if (!worksheet[xCell].v && !worksheet[yCell].v) {
    const x = treeStart.x + (treeIndex + 1) * deltaX;
    const y = (treeStart.y + treeEnd.y) / 2; // 假设树的纵坐标为两端点的平均值
    trees.push({ x, y });
    treeIndex++;
  }
}

// 将计算得到的树的坐标写入Excel文件
let rowIndex = 3;
trees.forEach((tree) => {
  const xCell = 'A' + rowIndex;
  const yCell = 'B' + rowIndex;

  worksheet[xCell] = { v: tree.x, t: 'n' };
  worksheet[yCell] = { v: tree.y, t: 'n' };

  rowIndex++;
});

// 将更新后的数据写回Excel文件
XLSX.writeFile(workbook, 'tree_data_updated.xlsx');
```



// ****************************

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <script src="https://unpkg.com/xlsx/dist/xlsx.full.min.js"></script>
    <title>js读取本地Excel内容</title>
    <style>
      table {
        border: 1px solid #ccc;
        border-collapse: collapse;
        width: 100%;
      }
      table td {
        border: 1px solid #ccc;
        text-align: center;
        height: 30px;
      }
      table tr:first-child {
        background: #ccc;
      }
    </style>
  </head>
  <body>
    <h2>js读取本地Excel内容</h2>
    <input type="file" id="excel" />
    <h3>Excel文件内容</h3>
    <div class="table"></div>
  </body>
</html>
<script>
  document.querySelector("#excel").onchange = function (e) {
    let file = e.target.files[0];
    readWorkbookFromLocalFile(file, function (workbook) {
      readWorkbook(workbook);
    });
  };

  // 读取本地excel文件，读取Excel文件对象
  function readWorkbookFromLocalFile(file, callback) {
    var reader = new FileReader();
    reader.onload = function (e) {
      var data = e.target.result;
      var workbook = XLSX.read(data, { type: "binary" });
      //   console.log(workbook);
      if (callback) callback(workbook);
    };
    reader.readAsBinaryString(file);
  }
  // 转成cvs 要注意Execel表格内容不能包含英文的,不然解析出来的数据格式会有问题
  function readWorkbook(workbook) {
    var sheetNames = workbook.SheetNames; // 工作表名称集合
    var worksheet = workbook.Sheets[sheetNames[0]]; // 这里我们只读取第一张sheet

    const treeStart = {
      x: parseFloat(worksheet["K2"].v),
      y: parseFloat(worksheet["L2"].v),
    };
    const treeEnd = {
      x: parseFloat(worksheet["K21"].v),
      y: parseFloat(worksheet["L21"].v),
    };
    // 计算未知的树的数量
    let treeCount = 0;
    for (let i = 3; i < 21; i++) {
      const xCell = "K" + i;
      const yCell = "L" + i;

      if (!worksheet[xCell] && !worksheet[yCell]) {
        treeCount++;
      }
    }
    // 假设树沿着水平方向均匀分布
    const deltaX = (treeEnd.x - treeStart.x) / (treeCount + 1);
    const deltaY = (treeEnd.y - treeStart.y) / (treeCount + 1);

    const X = treeStart.x + 19 * deltaX;

    // 计算每棵树的坐标
    const trees = [];
    let treeIndex = 0;
    for (let i = 3; i < 21; i++) {
      const xCell = "K" + i;
      const yCell = "L" + i;

      if (!worksheet[xCell] && !worksheet[yCell]) {
        const x = treeStart.x + (treeIndex + 1) * deltaX;
        const y = (treeStart.y + treeEnd.y) / 2; // 假设树的纵坐标为两端点的平均值
        trees.push({ x, y });
        treeIndex++;
      }
    }

    // 将计算得到的树的坐标写入Excel文件
    let rowIndex = 3;
    trees.forEach((tree) => {
      const xCell = "K" + rowIndex;
      const yCell = "L" + rowIndex;

      worksheet[xCell] = { v: tree.x, t: "n" };
      worksheet[yCell] = { v: tree.y, t: "n" };

      rowIndex++;
    });

    // ******************************

    let range = XLSX.utils.decode_range(worksheet["!ref"]); // 获取Excel数据范围
    let isStart = false
    let spaceNum = 0
    for (let R = 2; R <= range.e.r; R++) {
      // 遍历每一行
      if (worksheet[`K${R}`] && !isStart) {
        isStart = true
        treeStart = {
          x: parseFloat(worksheet[`K${R}`].v),
          y: parseFloat(worksheet[`K${R}`].v),
        }
        return
      }
      if(!worksheet[`K${R}`]) {
        spaceNum++
      }
      if(worksheet[`K${R}`] && isStart) {
        treeEnd = {
          x: parseFloat(worksheet[`K${R}`].v),
          y: parseFloat(worksheet[`K${R}`].v),
        }
        spaceNum = 0
      }

      //   let colX = XLSX.utils.decode_col(XLSX.utils.encode_cell({r: R, c: range.e.c - 1})); // 获取x的列号
      //   let colY = XLSX.utils.decode_col(XLSX.utils.encode_cell({r: R, c: range.e.c})); // 获取y的列号
      //   let x = worksheet[XLSX.utils.encode_cell({r: R, c: colX})]; // 获取当前行的x值
      //   let y = worksheet[XLSX.utils.encode_cell({r: R, c: colY})]; // 获取当前行的y值
      //   if (!x || !y) { // 如果x或者y为空，表示有一个未知的树
      //     let row = XLSX.utils.decode_row(XLSX.utils.encode_cell({r: R, c: 0})); // 获取行号
      //     // console.log(`空白的x和y：(${x.v || ''}, ${y.v || ''}), 行号：${row}`);
      //   }
    }

    // var csv = XLSX.utils.sheet_to_csv(worksheet);
    // console.log(csv);
    // XLSX.writeFile(workbook, './tree_data_updated.xlsx');
  }
</script>
```

okkk**********************

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <script src="https://unpkg.com/xlsx/dist/xlsx.full.min.js"></script>
    <title>js读取本地Excel内容</title>
    <style>
      table {
        border: 1px solid #ccc;
        border-collapse: collapse;
        width: 100%;
      }

      table td {
        border: 1px solid #ccc;
        text-align: center;
        height: 30px;
      }

      table tr:first-child {
        background: #ccc;
      }
    </style>
  </head>

  <body>
    <h2>读取本地Excel内容</h2>
    <input type="file" id="excel" />
    <div class="table"></div>
  </body>
</html>
<script>
  document.querySelector("#excel").onchange = function (e) {
    let file = e.target.files[0];
    readWorkbookFromLocalFile(file, function (workbook) {
      try {
        readWorkbook(workbook);
      } catch (error) {
        alert("出现错误：" + error.message);
      }
    });
  };

  // 读取本地excel文件，读取Excel文件对象
  function readWorkbookFromLocalFile(file, callback) {
    var reader = new FileReader();
    reader.onload = function (e) {
      var data = e.target.result;
      var workbook = XLSX.read(data, { type: "binary" });
      //   console.log(workbook);
      if (callback) callback(workbook);
    };
    reader.readAsBinaryString(file);
  }
  // 转成cvs 要注意Execel--------------------------------------------------表格内容不能包含英文的,不然解析出来的数据格式会有问题

  function readWorkbook(workbook) {
    var sheetNames = workbook.SheetNames; // 工作表名称集合
    var worksheet = workbook.Sheets[sheetNames[0]]; // 这里我们只读取第一张sheet

    let treeStart = {
      x: parseFloat(worksheet["K2"].v),
      y: parseFloat(worksheet["L2"].v),
    };
    let treeEnd = {
      x: parseFloat(worksheet["K21"].v),
      y: parseFloat(worksheet["L21"].v),
    };

    let range = XLSX.utils.decode_range(worksheet["!ref"]); // 获取Excel数据范围
    let isStart = false;
    let treeCount = 0;
    let startR = 0;
    let endR = 0;
    for (let R = 2; R <= range.e.r + 1; R++) {
      // for (let R = 2; R <= 307; R++) {
      // 遍历每一行
      if (worksheet[`K${R}`] && !worksheet[`K${R + 1}`] && !isStart) {
        startR = R;
        isStart = true;
        // 起始点
        treeStart = {
          x: parseFloat(worksheet[`K${R}`].v),
          y: parseFloat(worksheet[`L${R}`].v),
        };
        continue;
      }
      if (!worksheet[`K${R}`]) {
        treeCount++;
      }
      if (worksheet[`K${R}`] && isStart) {
        endR = R;
        // 结束点
        treeEnd = {
          x: parseFloat(worksheet[`K${R}`].v),
          y: parseFloat(worksheet[`L${R}`].v),
        };
        // 假设树沿着水平方向均匀分布
        const deltaX = (treeEnd.x - treeStart.x) / (treeCount + 1);
        const deltaY = (treeEnd.y - treeStart.y) / (treeCount + 1);
        // console.log('treeEnd.x'+treeEnd.x+'，R：'+R)
        // console.log('treeStart.x',treeStart.x)
        // console.log('treeCount',treeCount)
        // 计算每棵树的坐标
        const trees = [];
        let treeIndex = 0;
        for (let i = startR++; i < endR; i++) {
          const xCell = "K" + i;
          const yCell = "L" + i;

          if (!worksheet[xCell] && !worksheet[yCell]) {
            const x = treeStart.x + (treeIndex + 1) * deltaX;
            const y = treeStart.y + (treeIndex + 1) * deltaY; // 假设树的纵坐标为两端点的平均值
            trees.push({ x, y });
            treeIndex++;
          }
        }
        treeCount = 0;
        isStart = false;
        // console.log(trees);

        // 将计算得到的树的坐标写入Excel文件
        trees.forEach((tree) => {
          const xCell = "K" + startR;
          const yCell = "L" + startR;
          // console.log(xCell);
          worksheet[xCell] = { v: tree.x, t: "n" };
          worksheet[yCell] = { v: tree.y, t: "n" };

          startR++;
        });
        if (!worksheet[`K${R + 1}`]) {
          treeStart = {
            x: parseFloat(worksheet[`K${R}`].v),
            y: parseFloat(worksheet[`L${R}`].v),
          };
          isStart = true;
        }
      }
    }

    // var csv = XLSX.utils.sheet_to_csv(worksheet);
    // console.log(csv);
    // console.log(spaceNum)
    XLSX.writeFile(workbook, "./tree_data_updated.xlsx");
    alert('处理完成')
  }
</script>

```

