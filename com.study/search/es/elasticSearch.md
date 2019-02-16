````
    •QueryBuilders下有2种Query都可以用来匹配，一种是matchQuery，一种是termQuery。看上去很类似，注意区分用法。
    •termQuery能够完全匹配非中文的词，通常用于精确匹配。
    •而matchQuery则用于分词后的查询和模糊查询。
   •搜索的boolQuery相当于SQL的括号，能够实现And/Or规则的嵌套
matchQuery：会将搜索词分词，再与目标查询字段进行匹配，若分词中的任意一个词与目标字段匹配上，则可查询到。

termQuery：不会对搜索词进行分词处理，而是作为一个整体与目标字段进行匹配，若完全匹配，则可查询到
````
#### elasticsearch查询结果排序
````
org.elasticsearch.search.sort.SortBuilder使用
org.elasticsearch.search.sort.SortBuilder是一个抽象类，有4个子类

org.elasticsearch.search.sort.FieldSortBuilder 根据某属性值排序

org.elasticsearch.search.sort.GeoDistanceSortBuilder 根据地理位置排序

org.elasticsearch.search.sort.ScoreSortBuilder 根据score排序

org.elasticsearch.search.sort.ScriptSortBuilder 根据自定义脚本排序

可以通过SortBuilders的4个静态方法来分别生成SortBuilder的4个子类实例。可以通过SortBuiler的实例方法order(SortOrder order)来指定是升序还是倒序，默认是升序。

默认排序是按照Document的得分进行排序的。当检索结果集中的两个Document的具有相同的得分时，默认按照Document的ID对结果进行排序
````