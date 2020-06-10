# 因为不会解决给DTO分页 所以并未使用 仅记录

## 引入依赖

```xml
<!--分页插件-->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.2.13</version>
</dependency>
```



## 修改application.properties

```properties
#分页插件
pagehelper.helper-dialect=mysql
pagehelper.params=count=countSql
pagehelper.reasonable=true
pagehelper.support-methods-arguments=true
```



## 修改ProfileController

将以前的pagination修改为以下

```java
PageInfo<Question> pageInfo = questionService.ListByPageHelper(pageNum, size, user.getAccountId());
model.addAttribute("pageInfo", pageInfo)
```



## 修改QuestionService

```java
/*
     * 我发布的问题分页显示 使用pageHelp重构
     * */
    public PageInfo<Question> ListByPageHelper(Integer pageNum, Integer size, String accountId) {
        QuestionExample questionExample = new QuestionExample();
        questionExample.createCriteria()
                .andCreatorEqualTo(accountId);
        questionExample.setOrderByClause("gmt_create desc");
        PageHelper.startPage(pageNum, size); //这行代码的下一行会被分页
        List<Question> questions = questionMapper.selectByExample(questionExample);
        return new PageInfo<Question>(questions);
    }
```



## 修改前端问题展示部分

```html
<div class="media" th:each="question:${pageInfo.list}" th:if="${section} == 'question'">
                <div class="media-left">
                    <a th:href="@{'/question/'+${question.id}}">
                        <img class="media-object img-circle" th:src="${session.user.avatarUrl}">
                    </a>
                </div>
                <div class="media-body" >
                    <a th:href="@{'/question/'+${question.id}}">
                        <h4 class="media-heading" th:text="${question.title}"></h4>
                    </a>
<!--                    <span th:text="${question.description}"></span><br>-->
                    <span class="text-desc" th:text="${question.commentCount}+'个回复 | '+${question.viewCount}+'个浏览 | '+${#dates.format(question.gmtCreate,'yyyy/MM/dd HH:mm')}+'创建'"> </span>
                    <div>
                        <a th:href="'/publish/'+${question.id}" class="text-desc"><span class="glyphicon glyphicon-pencil" aria-hidden="true"th:if="${session.user}!=null and ${session.user.accountId} eq ${question.creator}" >编辑</span></a>
                    </div>
                </div>
            </div>
```



## 修改前端分页部分

```html
<nav aria-label="Page navigation">
    <ul class="pagination">
        <li th:if="!${pageInfo.isFirstPage}">
            <a th:href="@{'/profile/'+${section}}" aria-label="Previous">
                <span aria-hidden="true">&lt;&lt;</span>
            </a>
        </li>
        <li th:if="${pageInfo.hasPreviousPage}">
            <a th:href="@{'/profile/'+${section}(pageNum=${pageInfo.prePage})}" aria-label="Previous">
                <span aria-hidden="true">&lt;</span>
            </a>
        </li>
        <li th:each="page:${pageInfo.navigatepageNums}" th:class="${pageInfo.pageNum}==${page} ? 'active' : ''"><a th:href="@{'/profile/'+${section}(pageNum=${page})}" th:text="${page}">1</a></li>

        <li th:if="${pageInfo.hasNextPage}">
            <a th:href="@{'/profile/'+${section}(pageNum=${pageInfo.nextPage})}" aria-label="Previous">
                <span aria-hidden="true">&gt;</span>
            </a>
        </li>
        <li th:if="!${pageInfo.isLastPage}">
            <a th:href="@{'/profile/'+${section}(pageNum=${pageInfo.pages})}" aria-label="Previous">
                <span aria-hidden="true">&gt;&gt;</span>
            </a>
        </li>
    </ul>
</nav>
```



PageHelper中默认的PageInfo成员变量

```java
//当前页
private int pageNum;
//每页的数量
private int pageSize;
//当前页的数量
private int size;
//排序
private String orderBy;
 
//由于startRow和endRow不常用，这里说个具体的用法
//可以在页面中"显示startRow到endRow 共size条数据"
 
//当前页面第一个元素在数据库中的行号
private int startRow;
//当前页面最后一个元素在数据库中的行号
private int endRow;
//总记录数
private long total;
//总页数
private int pages;
//结果集
private List<T> list;
 
//第一页
private int firstPage;
//前一页
private int prePage;
//下一页
private int nextPage;
//最后一页
private int lastPage;
 
//是否为第一页
private boolean isFirstPage = false;
//是否为最后一页
private boolean isLastPage = false;
//是否有前一页
private boolean hasPreviousPage = false;
//是否有下一页
private boolean hasNextPage = false;
//导航页码数
private int navigatePages;
//所有导航页号
private int[] navigatepageNums;
```

[参考](https://blog.csdn.net/qq_27317475/article/details/81168241)

