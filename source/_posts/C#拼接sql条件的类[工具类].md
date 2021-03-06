---
title: "C#拼接sql条件的类[工具类]"
categories: [ "SQL" ]
tags: [ "c#" ]
draft: false
slug: "c-2"
date: "2019-03-25 10:29:00"
---

c#下实现拼接sql 工具类
<!--more-->
```csharp
using System;
using System.Collections.Generic;
using System.Data.SqlClient;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace WLB2016.LocalIntegrationTestProxy.util
{
    #region  public enum Comparison
    public enum Comparison
    {
        /// <summary>
        /// 等于号 =
        /// </summary>
        Equal,
        /// <summary>
        /// 不等于号 <>
        /// </summary>
        NotEqual,
        /// <summary>
        /// 大于号 >
        /// </summary>
        GreaterThan,
        /// <summary>
        /// 大于或等于 >=
        /// </summary>
        GreaterOrEqual,
        /// <summary>
        /// 小于 <
        /// </summary>
        LessThan,
        /// <summary>
        /// 小于或等于 <=
        /// </summary>
        LessOrEqual,
        /// <summary>
        /// 模糊查询 Like
        /// </summary>
        Like,
        /// <summary>
        /// 模糊查询  Not Like
        /// </summary>
        NotLike,
        /// <summary>
        /// is null
        /// </summary>
        IsNull,
        /// <summary>
        /// is not null
        /// </summary>
        IsNotNull,
        /// <summary>
        /// in
        /// </summary>
        In,
        /// <summary>
        /// not in
        /// </summary>
        NotIn,
        /// <summary>
        /// 左括号 （
        /// </summary>
        OpenParenthese,
        /// <summary>
        /// 右括号 )
        /// </summary>
        CloseParenthese,
        Between,
        StartsWith,
        EndsWith
    }
    #endregion

    public class ConditionHelper
    {
        #region 变量定义
        string parameterPrefix = "@";
        string parameterKey = "P";
        /// <summary>
        /// 用来拼接SQL语句
        /// </summary>
        StringBuilder conditionBuilder = new StringBuilder();
        /// <summary>
        /// 为True时表示字段为空或者Null时则不作为查询条件
        /// </summary>
        bool isExcludeEmpty = true;
        /// <summary>
        /// 是否生成带参数的sql
        /// </summary>
        bool isBuildParameterSql = true;
        /// <summary>
        /// 参数列表
        /// </summary>
        public List<SqlParameter> parameterList = new List<SqlParameter>();
        int index = 0;

        const string and = " AND ";
        const string or = " OR ";
        #endregion

        #region 构造函数

        /// <summary>
        /// 创建ConditionHelper对象
        /// </summary>
        /// <param name="isBuildParameterSql">是否生成带参数的sql</param>
        /// <param name="isExcludeEmpty">为True时表示字段为空或者Null时则不作为查询条件</param>
        public ConditionHelper(bool isBuildParameterSql = true, bool isExcludeEmpty = true)
        {
            this.isBuildParameterSql = isBuildParameterSql;
            this.isExcludeEmpty = isExcludeEmpty;
        }
        #endregion

        #region 公共方法
        /// <summary>
        /// 添加and 条件
        /// </summary>
        /// <param name="fieldName">字段名称</param>
        /// <param name="comparison">比较符类型</param>
        /// <param name="fieldValue">字段值</param>
        /// <returns>返回ConditionHelper</returns>
        public ConditionHelper AddAndCondition(string fieldName, Comparison comparison, params object[] fieldValue)
        {
            conditionBuilder.Append(and);
            this.AddCondition(fieldName, comparison, fieldValue);
            return this;
        }

        /// <summary>
        /// 添加or条件
        /// </summary>
        /// <param name="fieldName">字段名称</param>
        /// <param name="comparison">比较符类型</param>
        /// <param name="fieldValue">字段值</param>
        /// <returns>返回ConditionHelper</returns>
        public ConditionHelper AddOrCondition(string fieldName, Comparison comparison, params object[] fieldValue)
        {
            conditionBuilder.Append(or);
            this.AddCondition(fieldName, comparison, fieldValue);
            return this;
        }

        /// <summary>
        /// 添加and+左括号+条件  
        /// </summary>
        /// <param name="comparison">比较符类型</param>
        /// <param name="fieldName">字段名称</param>
        /// <param name="fieldValue">字段值，注：Between时，此字段必须填两个值</param>
        /// <returns>返回ConditionHelper</returns>
        public ConditionHelper AddAndOpenParenthese(string fieldName, Comparison comparison, params object[] fieldValue)
        {
            this.conditionBuilder.AppendFormat("{0}{1}", and, GetComparisonOperator(Comparison.OpenParenthese));
            this.AddCondition(fieldName, comparison, fieldValue);
            return this;
        }

        /// <summary>
        /// 添加or+左括号+条件
        /// </summary>
        /// <returns></returns>
        /// <param name="comparison">比较符类型</param>
        /// <param name="fieldName">字段名称</param>
        /// <param name="fieldValue">字段值，注：Between时，此字段必须填两个值</param>
        /// <returns>返回ConditionHelper</returns>
        public ConditionHelper AddOrOpenParenthese(string fieldName, Comparison comparison, params object[] fieldValue)
        {
            this.conditionBuilder.AppendFormat("{0}{1}", or, GetComparisonOperator(Comparison.OpenParenthese));
            this.AddCondition(fieldName, comparison, fieldValue);
            return this;
        }

        /// <summary>
        /// 添加右括号
        /// </summary>
        /// <returns></returns>
        public ConditionHelper AddCloseParenthese()
        {
            this.conditionBuilder.Append(GetComparisonOperator(Comparison.CloseParenthese));
            return this;
        }

        /// <summary>
        /// 添加条件
        /// </summary>
        /// <param name="comparison">比较符类型</param>
        /// <param name="fieldName">字段名称</param>
        /// <param name="fieldValue">字段值，注：Between时，此字段必须填两个值</param>
        /// <returns>返回ConditionHelper</returns>
        public ConditionHelper AddCondition(string fieldName, Comparison comparison, params object[] fieldValue)
        {
            //如果选择IsExcludeEmpty为True,并且该字段为空值的话则跳过
            if (isExcludeEmpty && string.IsNullOrEmpty(fieldValue.ToString()))
                return this;

            switch (comparison)
            {
                case Comparison.Equal:
                case Comparison.NotEqual:
                case Comparison.GreaterThan:
                case Comparison.GreaterOrEqual:
                case Comparison.LessThan:
                case Comparison.LessOrEqual:
                    this.conditionBuilder.AppendFormat("{0}{1}{2}", GetFieldName(fieldName), GetComparisonOperator(comparison), GetFieldValue(fieldValue[0]));
                    break;
                case Comparison.IsNull:
                case Comparison.IsNotNull:
                    this.conditionBuilder.AppendFormat("{0}{1}", GetFieldName(fieldName), GetComparisonOperator(comparison));
                    break;
                case Comparison.Like:
                case Comparison.NotLike:
                    this.conditionBuilder.AppendFormat("{0}{1}{2}", GetFieldName(fieldName), GetComparisonOperator(comparison), GetFieldValue(string.Format("%{0}%", fieldValue[0])));
                    break;
                case Comparison.In:
                case Comparison.NotIn:
                    this.conditionBuilder.AppendFormat("{0}{1}({2})", GetFieldName(fieldName), GetComparisonOperator(comparison), string.Join(",", GetFieldValue(fieldValue)));
                    break;
                case Comparison.StartsWith:
                    this.conditionBuilder.AppendFormat("{0}{1}{2}", GetFieldName(fieldName), GetComparisonOperator(comparison), GetFieldValue(string.Format("{0}%", fieldValue[0])));
                    break;
                case Comparison.EndsWith:
                    this.conditionBuilder.AppendFormat("{0}{1}{2}", GetFieldName(fieldName), GetComparisonOperator(comparison), GetFieldValue(string.Format("%{0}", fieldValue[0])));
                    break;
                case Comparison.Between:
                    this.conditionBuilder.AppendFormat("{0}{1}{2} AND {3}", GetFieldName(fieldName), GetComparisonOperator(comparison), GetFieldValue(fieldValue[0]), GetFieldValue(fieldValue[1]));
                    break;
                default:
                    throw new Exception("条件为定义");
            }
            return this;
        }

        public override string ToString()
        {
            return this.conditionBuilder.ToString();
        }

        #endregion

        #region 私有方法
        /// <summary>
        /// 取得字段值
        /// </summary>
        /// <param name="fieldValue"></param>
        /// <returns></returns>
        private string GetFieldValue(params object[] fieldValue)
        {
            if (isBuildParameterSql == false)
            {
                if (fieldValue.Length < 2)
                {
                    return string.Format("'{0}'", fieldValue[0]);
                }
                else
                {
                    return string.Format("'{0}'", string.Join("','", fieldValue));
                }
            }
            else
            {
                if (fieldValue.Length < 2)
                {
                    return AddParameter(fieldValue[0]);
                }
                else
                {
                    List<string> parameterNameList = new List<string>();
                    foreach (var value in fieldValue)
                    {
                        parameterNameList.Add(AddParameter(value));
                    }
                    return string.Join(",", parameterNameList);
                }
            }
        }

        /// <summary>
        /// 添加参数
        /// </summary>
        /// <param name="fieldValue"></param>
        /// <returns></returns>
        private string AddParameter(object fieldValue)
        {
            index++;
            string parameterName = string.Format("{0}{1}{2}", parameterPrefix, parameterKey, index);
            parameterList.Add(new SqlParameter()
            {
                ParameterName = parameterName,
                Value = fieldValue
            });
            return parameterName;
        }

        private string GetFieldName(string fieldName)
        {
            return string.Format("[{0}]", fieldName);
        }
        private static string GetComparisonOperator(Comparison comparison)
        {
            string result = string.Empty;
            switch (comparison)
            {
                case Comparison.Equal:
                    result = " = ";
                    break;
                case Comparison.NotEqual:
                    result = " <> ";
                    break;
                case Comparison.GreaterThan:
                    result = " > ";
                    break;
                case Comparison.GreaterOrEqual:
                    result = " >= ";
                    break;
                case Comparison.LessThan:
                    result = " < ";
                    break;
                case Comparison.LessOrEqual:
                    result = " <= ";
                    break;
                case Comparison.Like:
                case Comparison.StartsWith:
                case Comparison.EndsWith:
                    result = " LIKE ";
                    break;
                case Comparison.NotLike:
                    result = " NOT LIKE ";
                    break;
                case Comparison.IsNull:
                    result = " IS NULL ";
                    break;
                case Comparison.IsNotNull:
                    result = " IS NOT NULL ";
                    break;
                case Comparison.In:
                    result = " IN ";
                    break;
                case Comparison.NotIn:
                    result = " NOT IN ";
                    break;
                case Comparison.OpenParenthese:
                    result = " (";
                    break;
                case Comparison.CloseParenthese:
                    result = ") ";
                    break;
                case Comparison.Between:
                    result = " BETWEEN ";
                    break;
            }
            return result;
        }
        #endregion

    }
}

```
比如说要实现这样的一个例子： `UserName In ('张三','李四','王五') and Age between 1 and 17 and (Gender='Male' or Gender='Female')`

实现代码：
```csharp
ConditionHelper helper = new ConditionHelper(false);
helper.AddCondition("UserName", Comparison.In, "张三", "李四", "王五")
      .AddAndCondition("Age",Comparison.Between,1,17)
      .AddAndOpenParenthese("Gender",Comparison.Equal,"Male")
      .AddOrCondition("Gender",Comparison.Equal,"Female")
      .AddCloseParenthese();
 string condition=helper.ToString();
```
还有要提一下的是这个类中的isExcludeEmpty变量，这个是借鉴了园子里伍华聪的想法，由于是很早以前看的，具体是哪一篇文章就不太清楚了，有兴趣的可以去他博客[http://www.cnblogs.com/wuhuacong/](http://www.cnblogs.com/wuhuacong/)里找下看。这变量在这有什么用呢？不要小看这小小的变量，它让我们在实际中少了很多重复的代码。比如界面上有一个条件文本框txtUserName,那我们一般拼接条件如下：
```csharp
if (!string.IsNullOrEmpty(txtUserName.Text.Trim())
{
  condition = string.Format("UserName like '%{0}%'", txtUserName.Text.Trim()） 
}
```
简单说就是每次在拼接条件时都要判断文本框里的值是否为空，只有在不为空的情况才加入条件里去。 现在在ConditonHelper里加了isExcludeEmpty变量，我们在使用的时候就不要加判断了，在ConditionHelper中拼接条件时它会自动去判断，是不是这样让代码变得更简洁？
