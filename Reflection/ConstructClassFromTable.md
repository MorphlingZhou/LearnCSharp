# 利用attribute通过表格数据创建自定义结构体

```csharp
    /// <summary>
    /// 利用属性 反射 Attribute来完成一个表格的读取
    /// 输入 string[]
    /// 输出 自定义的结构类型
    /// </summary>
    public class ConstructClassFromTable
    {
        public void DoMain()
        {
            string[] s = { "Invoker", "27", "true", "178.8" };

            TableData t = new TableData();

            for (int i = 0; i < s.Length; i++)
                SetData(s[i], i, ref t);

            Console.WriteLine("just for break");
        }

        public void SetData<T>(string value, int index, ref T classType)
        {
            Type t = classType.GetType();
            PropertyInfo[] p = t.GetProperties();
            object valueObj = new object();

            foreach(PropertyInfo pi in p)
            {
                var ai = (Field)Attribute.GetCustomAttribute(pi, typeof(Field));

                if (ai == null)
                    continue;

                if (ai.tableIndex == index)
                    ConvertAndSetValue(value, pi, ref classType, ref valueObj);
            }
        }

        public void ConvertAndSetValue<T>(string value, PropertyInfo pi, ref T obj, ref object valueObj)
        {
            if (pi.PropertyType == typeof(int))
                valueObj = int.Parse(value);

            if (pi.PropertyType == typeof(float))
                valueObj = float.Parse(value);

            if (pi.PropertyType == typeof(bool))
                valueObj = bool.Parse(value);

            if (pi.PropertyType == typeof(string))
                valueObj = value;

            pi.SetValue(obj, valueObj, null);
        }
    }

    public class TableData
    {
        [Field(tableIndex = 0, tableName = "PeopleName", tablePath = "./People.txt")]
        public string Name { get; set; }

        [Field(tableIndex = 1, tableName = "PeopleAge", tablePath = "./People.txt")]
        public int Age { get; set; }
        
        [Field(tableIndex = 2, tableName = "PeopleSex", tablePath = "./People.txt")]
        public bool Sex { get; set; }

        [Field(tableIndex = 3, tableName = "PeopleHeight", tablePath = "./People.txt")]
        public float Height { get; set; }
    }

    [AttributeUsage(AttributeTargets.Property)]
    public class Field : Attribute
    {
        public int tableIndex { get; set; }
        public string tableName { get; set; }
        public string tablePath { get; set; }
    }    
```