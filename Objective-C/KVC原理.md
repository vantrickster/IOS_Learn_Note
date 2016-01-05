当KVC调用之后，到底是怎样获取和设置类成员值的？？

### `setValue:forKey`的搜索方式

***

* 首先搜索`set<Key>:`方法。如果成员用`@property`，`@synthsize`处理，因为`@synthsize`告诉编译器自动生成`set<Key>:`格式的setter方法，所以这种情况下会直接搜索到。注意，这里的<Key>指的是成员名，而且首字母大写。下同。
* 上面的setter方法没有找到，如果类方法`accessInstanceVariablesDirectly`返回YES(注：这是`NSKeyValueCodingCatogery`中实现的类方法，默认实现为返回YES)。那么按\_\<key\>, _is\<Key\>, \<Key\>, is\<Key\>的顺序搜索成员名。
* 如果找到，就设置成员的值；如果没有，就调用`setValue:forUndefinedKey`。


### `valueForKey`的搜索方式

***

* 首先按get<Key>, <Key>, is<Key>的顺序查找getter方法，找到直接调用。如果是bool、int等内建值类型，会做NSNumber的转换。
* 上面的getter没有找到，查找countOf\<Key\>, objectIn\<Key\>AtIndex, \<Key\>AtIndexes格式的方法。
* 如果countOf\<Key\>和另外两个方法中的一个找到，就会返回一个可以相应NSArray所有方法的代理集合(collection proxy object)。发送给这个代理集合的NSArray消息方法，就会以countOf\<Key\>, objectIn\<Key\>AtIndex, \<Key\>AtIndexes这几个方法组合的形式调用。还有一个可选的get\<Key\>:range:方法。
* 如果还没有查到，那么查找countOf\<Key\>, enumeratorOf\<Key\>, memberOf\<Key\>:格式的方法。
* 如果以上三个方法都找到，那么返回一个可以相应NSSet所有方法的代理集合(collection proxy object)。发送给这个代理集合的NSSet消息方法，就会以countOf\<Key\>, enumeratorOf\<Key\>, memberOf\<Key\>:组合的形式调用。
* 如果还是没有查到，如果类方法`accessInstanceVariablesDirectly`返回YES，那么按_\<Key\>, _is\<Key\>, \<Key\>, is\<Key\>的顺序直接搜搜成员名。
* 如果还没有查到，调用`valueForUndefinedKey:`。

