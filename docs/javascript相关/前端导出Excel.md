# 前端导出和读取 excel

> 安装依赖 excel.js 和 file-saver.js

`npm install exceljs file-saver --save`

> 封装 excel 构造类

```js
/**
 * ExceljsInstance 类用于处理 Excel 文件的读取和下载操作。
 */
class ExceljsInstance {
  /**
   * 初始化 ExceljsInstance 实例，创建一个新的 Excel 工作簿。
   */
  constructor() {
    this.initExcel();
  }

  /**
   * 初始化 Excel 工作簿。
   */
  initExcel() {
    this.workbook = new ExcelJS.Workbook();
  }

  /**
   * 读取 Excel 文件并解析指定工作表的数据。
   * @param {File} file - 要读取的 Excel 文件。
   * @param {number} [sheetIdx=1] - 工作表索引，默认为 1。
   * @returns {Promise<void>} - 完成读取和解析操作的 Promise。
   */
  readExcelFile(file, sheetIdx = 1) {
    return new Promise((resolve, reject) => {
      if (!file) {
        return reject(new Error("No file provided."));
      }

      // 验证文件类型
      if (
        !file.type.includes(
          "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
        )
      ) {
        return reject(
          new Error("Invalid file type. Only .xlsx files are supported.")
        );
      }

      const reader = new FileReader();
      reader.onload = (e) => {
        const data = e.target.result;
        this.workbook.xlsx
          .load(data)
          .then((workbook) => {
            if (!workbook.getWorksheet(sheetIdx)) {
              return reject(
                new Error(`Sheet index ${sheetIdx} does not exist.`)
              );
            }

            const worksheet = workbook.getWorksheet(sheetIdx);
            const header = worksheet.getRow(1);
            const headerValues = ["empt", "supplier", "endingPayable"]; // 考虑从配置获取
            const rows = worksheet.getSheetValues();

            /**
             * 将工作表的数据转换为对象数组。
             * @type {Array<object>}
             */
            const rowsTable = rows.map((row) => {
              const rowObj = {};
              row.forEach((item, index) => {
                if (index === 0) {
                  item = item.trim();
                }
                rowObj[headerValues[index]] = item;
              });
              return rowObj;
            });

            this.excelData = rowsTable;
            resolve();
          })
          .catch((error) => {
            reject(error);
          });
      };

      reader.onerror = (error) => {
        reject(new Error("Failed to read file."));
      };

      reader.readAsArrayBuffer(file);
    });
  }

  /**
   * 下载 Excel 文件。
   * @param {Array} columns - 列定义数组。
   * @param {Array} tableData - 表格数据数组。
   * @param {string} fileName - 下载的文件名。
   */
  async downloadExcel(columns, tableData, fileName) {
    try {
      const worksheet = this.workbook.addWorksheet("Sheet1");
      worksheet.columns = columns;
      worksheet.addTable({
        ref: "A1",
        columns: columns,
        rows: tableData.map((item) => Object.values(item)),
      });

      const buffer = await workbook.xlsx.writeBuffer();
      saveAs(new Blob([buffer]), `${fileName}.xlsx`);
    } catch (error) {
      console.log("Download failed:", error);
      // 可以考虑更优雅的错误处理方式，如用户通知或错误日志记录
    }
  }
}

export default ExceljsInstance;
```
