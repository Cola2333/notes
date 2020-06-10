# 第一步 创建计时器

查看官方文档https://spring.io/guides/gs/scheduling-tasks/

新建包 life.usc.study.schedule

新建类 HotTagTasks

```
//lombox的 可以不用再去写final Logger log = LoggerFactory.getLogger(ScheduledTasks.class)
@Slf4j
```

```
//一旦使用关于Spring的注解出现在类里，例如在实现类中用到了@Autowired注解，被注解的这个类是从Spring容器中取出来的，那调用的实现类也需要被Spring容器管理，加上@Component
@Component
```

HotTagTasks添加代码 定时更新写法：https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/scheduling/support/CronSequenceGenerator.html

```java
package life.usc.study.schedule;

import lombok.extern.slf4j.Slf4j;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.util.Date;

@Component
@Slf4j
public class HotTagTasks {
    @Scheduled(fixedRate = 5000)// 每五秒更新一次
    @Scheduled(cron = "0 0 1 * * *")// 每天凌晨1点更新
    public void reportCurrentTime() {
        log.info("The time is now {}", new Date());
    }
}

```

StudyApplication中添加代码

```
@EnableScheduling
```



# 第二步 获取所有问题id 测试计时器

```java
package life.usc.study.schedule;

import life.usc.study.mapper.QuestionMapper;
import life.usc.study.model.Question;
import life.usc.study.model.QuestionExample;
import lombok.extern.slf4j.Slf4j;
import org.apache.ibatis.session.RowBounds;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

@Component
@Slf4j
public class HotTagTasks {

    @Autowired
    QuestionMapper questionMapper;

    @Scheduled(fixedRate = 1000)// 每五秒更新一次
//    @Scheduled(cron = "0 0 1 * * *")// 每天凌晨1点更新
    public void hotTagSchedule() {
        int offset = 0;
        int limit = 5;
        log.info("hotTagSchedule start {}", new Date());
        List<Question> list = new ArrayList<>();
        while (offset == 0 || list.size() == limit) { //这种情况下才可能会有下一页
            list = questionMapper.selectByExampleWithRowbounds(new QuestionExample(), new RowBounds(offset, limit));
            for (Question question : list) {
                log.info("list question: {}", question.getId());
            }
            offset += limit;
        }
        log.info("hotTagSchedule stop {}", new Date());

    }
}
```



# 第三步 梳理逻辑

遍历所有问题 拿到每个问题的标签 Java, c...

计算每个标签的问题数 回复数

增加权重priority = 5 * question + comment; 



新建类HotTagCache

```java
//对于 final 变量，如果是基本数据类型，则其数值在初始化后便不能更改；如果是引用类型，对其进行初始化后，便不能再指向另一个对象
 private final static Map<String, Integer> tags = new HashMap<>();
//用 final 修饰类，表明这个类不能被继承，此时类中的所有成员方法，都会被隐式地指定为 final 方法

```

```java
package life.usc.study.cache;

import java.util.HashMap;
import java.util.Map;

public class HotTagCache {
    private final static Map<String, Integer> tags = new HashMap<>();
    public static synchronized Map<String, Integer> getTags() {
        return tags;
    }
}
```

修改HotTagTasks

```java
package life.usc.study.schedule;

import life.usc.study.cache.HotTagCache;
import life.usc.study.mapper.QuestionMapper;
import life.usc.study.model.Question;
import life.usc.study.model.QuestionExample;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.StringUtils;
import org.apache.ibatis.session.RowBounds;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.sql.SQLOutput;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;
import java.util.Map;

@Component
@Slf4j
public class HotTagTasks {

    @Autowired
    QuestionMapper questionMapper;

    @Scheduled(fixedRate = 1000)// 每五秒更新一次
//    @Scheduled(cron = "0 0 1 * * *")// 每天凌晨1点更新
    public void hotTagSchedule() {
        int offset = 0;
        int limit = 5;
        log.info("hotTagSchedule start {}", new Date());
        List<Question> list = new ArrayList<>();
        while (offset == 0 || list.size() == limit) { //可能会有下一页
            list = questionMapper.selectByExampleWithRowbounds(new QuestionExample(), new RowBounds(offset, limit));
            for (Question question : list) {
                Map<String, Integer> priorities = HotTagCache.getTags();
                String[] tags = StringUtils.split(question.getTag(), ",");
                for (String tag : tags) {
                    if (priorities.containsKey(tag)) {
                        priorities.put(tag, priorities.get(tag) + 5 + question.getCommentCount());
                    }
                    else {
                        priorities.put(tag, 5 + question.getCommentCount());
                    }
                }
            }
            offset += limit;
        }
        HotTagCache.getTags().forEach(
                (k, v) -> {
                    System.out.print(k);
                    System.out.print(":");
                    System.out.print(v);
                    System.out.println();
                }
        );
        log.info("hotTagSchedule stop {}", new Date());

    }
}
```

执行后发现每次都会重新计算 然后累加 这是不对的 所以我们需要重新修改HotTagCache HotTagTasks

修改HotTagCache 

```java
package life.usc.study.cache;

import lombok.Data;
import org.springframework.stereotype.Component;

import java.util.HashMap;
import java.util.Map;

@Component
@Data
public class HotTagCache {
    private Map<String, Integer> tags = new HashMap<>();
}
```

修改HotTagTasks**感觉这个分页没什么用啊~**

```Java
package life.usc.study.schedule;

import life.usc.study.cache.HotTagCache;
import life.usc.study.mapper.QuestionMapper;
import life.usc.study.model.Question;
import life.usc.study.model.QuestionExample;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.StringUtils;
import org.apache.ibatis.session.RowBounds;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.sql.SQLOutput;
import java.util.*;

@Component
@Slf4j
public class HotTagTasks {

    @Autowired
    QuestionMapper questionMapper;

    @Autowired
    HotTagCache hotTagCache;

    @Scheduled(fixedRate = 1000)// 每五秒更新一次
//    @Scheduled(cron = "0 0 1 * * *")// 每天凌晨1点更新
    public void hotTagSchedule() {
        int offset = 0;
        int limit = 5;
        log.info("hotTagSchedule start {}", new Date());
        List<Question> list = new ArrayList<>();
        Map<String, Integer> priorities = new HashMap<>();
        while (offset == 0 || list.size() == limit) { //可能会有下一页
            list = questionMapper.selectByExampleWithRowbounds(new QuestionExample(), new RowBounds(offset, limit));
            for (Question question : list) {
                String[] tags = StringUtils.split(question.getTag(), ",");
                for (String tag : tags) {
                    if (priorities.containsKey(tag)) {
                        priorities.put(tag, priorities.get(tag) + 5 + question.getCommentCount());
                    }
                    else {
                        priorities.put(tag, 5 + question.getCommentCount());
                    }
                }
            }
            hotTagCache.setTags(priorities);
            offset += limit;
        }
        hotTagCache.getTags().forEach(
                (k, v) -> {
                    System.out.print(k);
                    System.out.print(":");
                    System.out.print(v);
                    System.out.println();
                }
        );
        log.info("hotTagSchedule stop {}", new Date());

    }
}
```



# 第四步 整合堆排序

到此  我们已经通过HotTagTasks 完成了对HotTagCache中TagsPriorities的赋值

下面直接创建PriorityQueue



新建HotTagDTO

```java
package life.usc.study.dto;

import lombok.Data;

@Data
public class HotTagDTO {
    private String name;
    private Integer priority;
}
```

为HotTagCache增加updateTagsPriorities()方法

```java
package life.usc.study.cache;

import life.usc.study.dto.HotTagDTO;
import lombok.Data;
import org.springframework.stereotype.Component;

import java.util.*;

@Component
@Data
/*
* 用于存储每个Tag的Priority
* */
public class HotTagCache {
    private Map<String, Integer> tagsPriorities = new HashMap<>(); //接受HotTagTasks传过来的所有tag的priority
    private LinkedList<String> topHots = new LinkedList<>(); //存储PriorityQueue中的topHots

    public void updateTagsPriorities(Map<String, Integer> tagsPriorities) {
        topHots = new LinkedList<>(); //防止累加
		int max = 5; //生成最热问题的个数
        PriorityQueue<HotTagDTO> pq = new PriorityQueue<>(3, ((o1, o2) -> o1.getPriority() - o2.getPriority()));
        tagsPriorities.forEach((name, priority) -> {
            HotTagDTO hotTagDTO = new HotTagDTO();
            hotTagDTO.setName(name);
            hotTagDTO.setPriority(priority);
            pq.add(hotTagDTO);
            if (pq.size() > 3) {
                pq.poll();
            }
        });

        for (HotTagDTO hotTagDTO : pq) {
            topHots.addFirst(hotTagDTO.getName());
        }
        System.out.println(topHots);
    }
}


```

在hotTagTask中调用新增的updateTag方法

```java
package life.usc.study.schedule;

import life.usc.study.cache.HotTagCache;
import life.usc.study.mapper.QuestionMapper;
import life.usc.study.model.Question;
import life.usc.study.model.QuestionExample;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang3.StringUtils;
import org.apache.ibatis.session.RowBounds;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.util.*;

@Component
@Slf4j
/*
 * 为HotTagCache中的TagsPriorities赋值
 * */
public class HotTagTasks {

    @Autowired
    QuestionMapper questionMapper;

    @Autowired
    HotTagCache hotTagCache;

    @Scheduled(fixedRate = 10000)// 每五秒更新一次
//    @Scheduled(cron = "0 0 1 * * *")// 每天凌晨1点更新

    /*
    * 为HotTagCache中的TagsPriorities赋值
    * */
    public void hotTagSchedule() {
        int offset = 0;
        int limit = 5;
        log.info("hotTagSchedule start {}", new Date());
        List<Question> list = new ArrayList<>();
        Map<String, Integer> priorities = new HashMap<>();
        while (offset == 0 || list.size() == limit) { //可能会有下一页
            list = questionMapper.selectByExampleWithRowbounds(new QuestionExample(), new RowBounds(offset, limit));
            for (Question question : list) {
                String[] tags = StringUtils.split(question.getTag(), ",");
                for (String tag : tags) {
                    if (priorities.containsKey(tag)) {
                        priorities.put(tag, priorities.get(tag) + 5 + question.getCommentCount());
                    }
                    else {
                        priorities.put(tag, 5 + question.getCommentCount());
                    }
                }
            }
            offset += limit;
        }
        hotTagCache.setTagsPriorities(priorities);
        hotTagCache.getTagsPriorities().forEach(
                (k, v) -> {
                    System.out.print(k);
                    System.out.print(":");
                    System.out.print(v);
                    System.out.println();
                }
        );

        hotTagCache.updateTags(priorities);

        log.info("hotTagSchedule stop {}", new Date());

    }
}
```



# 第五步 页面回显

修改indexController 引入并使用HotTagCache 将其添加进入model

```java
package life.usc.study.controller;

import life.usc.study.cache.HotTagCache;
import life.usc.study.dto.PaginationDTO;
import life.usc.study.service.QuestionService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

import javax.servlet.http.HttpServletRequest;
import java.util.LinkedList;

@Controller
public class IndexController {

    @Autowired
    private QuestionService questionService;

    @Autowired
    private HotTagCache hotTagCache;

    @GetMapping("/")
    public String index(HttpServletRequest request,
                        Model model,
                        @RequestParam(name = "pageNum", defaultValue = "1") Integer pageNum,
                        @RequestParam(name = "size", defaultValue = "5") Integer size,
                        @RequestParam(name= "search", required = false) String search) {

        PaginationDTO pagination = questionService.show(pageNum, size, search);
        LinkedList<String> topHots = hotTagCache.getTopHots();
        model.addAttribute("questionList", pagination); //其实应该叫name应该是pagination 懒得改了
        model.addAttribute("search", search);
        model.addAttribute("topHots", topHots);

        return "index";
    }
}
```

实现点击index页面的topHots可以显示标签为该topHot的所有问题

1. 修改index.html 热门话题部分 和分页部分 使其能够接受参数

2. 修改indexController 增加接受topHot参数 并经其传入questionService.show()方法

   ```java
   package life.usc.study.controller;
   
   import life.usc.study.cache.HotTagCache;
   import life.usc.study.dto.PaginationDTO;
   import life.usc.study.service.QuestionService;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Controller;
   import org.springframework.ui.Model;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RequestParam;
   
   import javax.servlet.http.HttpServletRequest;
   import java.util.LinkedList;
   
   @Controller
   public class IndexController {
   
       @Autowired
       private QuestionService questionService;
   
       @Autowired
       private HotTagCache hotTagCache;
   
       @GetMapping("/")
       public String index(HttpServletRequest request,
                           Model model,
                           @RequestParam(name = "pageNum", defaultValue = "1") Integer pageNum,
                           @RequestParam(name = "size", defaultValue = "5") Integer size,
                           @RequestParam(name= "search", required = false) String search,
                           @RequestParam(name= "topHot", required = false) String topHot) {
   
           PaginationDTO pagination = questionService.show(pageNum, size, search, topHot);
           LinkedList<String> topHots = hotTagCache.getTopHots();
           model.addAttribute("questionList", pagination); //其实应该叫name应该是pagination 懒得改了
           model.addAttribute("search", search);
           model.addAttribute("topHots", topHots);
   
           return "index";
       }
   }
   ```

3. 进入questionService.show()进行重构

   1. 重构QuestionQueryDTO, 增加topHot属性

      ```java
      package life.usc.study.dto;
      
      import lombok.Data;
      
      @Data
      public class QuestionQueryDTO {
          private Integer offset;
          private Integer size;
          private String search;
          private String topHot;
      }
      ```

   2. 重构方法questionService.show() 增加questionQueryDTO.setTopHot(topHot);

      ```java
      public PaginationDTO show(Integer pageNum, Integer size, String search, String topHot) {
          if (StringUtils.isNotBlank(search)) {
              String[] tags = StringUtils.split(search, " ");
              search = Arrays.stream(tags).collect(Collectors.joining(" | "));
          }
          
          QuestionQueryDTO questionQueryDTO = new QuestionQueryDTO();
          questionQueryDTO.setSearch(search);
          questionQueryDTO.setTopHot(topHot);
          Integer totalCount = (int)questionExtMapper.countBySearch(questionQueryDTO);
          //Integer totalCount = questionMapper.countTotal();
          Integer totalPage;
      
          /*
          * 计算totalPage
          * */
          if (totalCount % size == 0) {
              totalPage = totalCount / size; //totalCount是总问题数 size是每页展示的问题数
          }else {
              totalPage = totalCount / size + 1;
          }
      
          /*
           * 防止页面数超过总页面数 重新给pageNum赋值
           **/
          if (pageNum > totalPage) {
              pageNum = totalPage;
          }else if (pageNum < 1){
              pageNum = 1;
          }
      
          Integer offset = (pageNum - 1) * size;// limit子句中的第一个参数
      
          questionQueryDTO.setOffset(offset);
          questionQueryDTO.setSize(size);
          List<Question> questions = questionExtMapper.selectBySearch(questionQueryDTO);
          //List<Question> questions = questionMapper.getAllQuestions(offset, size);
          List<QuestionDTO> questionDTOS = new ArrayList<>();// 用于如果不要pagination的话 可以用这个显示所有question
          for (Question question : questions) {
              UserExample userExample = new UserExample();
              userExample.createCriteria()
                      .andAccountIdEqualTo(question.getCreator());
              List<User> users = userMapper.selectByExample(userExample);
              User user = users.get(0);
              QuestionDTO questionDTO = new QuestionDTO();
              BeanUtils.copyProperties(question, questionDTO);
              questionDTO.setUser(user);
              questionDTOS.add(questionDTO);
          }
      
          /*
          * 得到了所需的全部属性 一起赋值
          * */
          PaginationDTO<QuestionDTO> paginationDTO = new PaginationDTO();
          paginationDTO.setPagination(questionDTOS, pageNum, totalPage);
          return paginationDTO;
      }
      ```

   3. 重构questionExtMapper.countBySearch(), questionExtMapper.selectBySearch()方法

      去QuestionExtMapper.xml下修改

      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
      <mapper namespace="life.usc.study.mapper.QuestionExtMapper">
        <resultMap id="BaseResultMap" type="life.usc.study.model.Question">
          <!--
            WARNING - @mbg.generated
            This element is automatically generated by MyBatis Generator, do not modify.
            This element was generated on Fri Apr 17 17:31:16 PDT 2020.
          -->
          <id column="ID" jdbcType="INTEGER" property="id" />
          <result column="TITLE" jdbcType="VARCHAR" property="title" />
          <result column="GMT_CREATE" jdbcType="BIGINT" property="gmtCreate" />
          <result column="GMT_MODIFIED" jdbcType="BIGINT" property="gmtModified" />
          <result column="CREATOR" jdbcType="VARCHAR" property="creator" />
          <result column="COMMENT_COUNT" jdbcType="INTEGER" property="commentCount" />
          <result column="VIEW_COUNT" jdbcType="INTEGER" property="viewCount" />
          <result column="LIKE_COUNT" jdbcType="INTEGER" property="likeCount" />
          <result column="TAG" jdbcType="VARCHAR" property="tag" />
          <result column="COLUMN_11" jdbcType="INTEGER" property="column11" />
        </resultMap>
        <resultMap extends="BaseResultMap" id="ResultMapWithBLOBs" type="life.usc.study.model.Question">
          <!--
            WARNING - @mbg.generated
            This element is automatically generated by MyBatis Generator, do not modify.
            This element was generated on Fri Apr 17 17:31:16 PDT 2020.
          -->
          <result column="DESCRIPTION" jdbcType="CLOB" property="description" />
        </resultMap>
      
        <update id="incViewCount" parameterType="life.usc.study.model.Question">
          update QUESTION
          set VIEW_COUNT = VIEW_COUNT + 1
          where ID = #{id}
        </update>
      
        <update id="incCommentCount" parameterType="life.usc.study.model.Question">
          update QUESTION
          set QUESTION.COMMENT_COUNT = COMMENT_COUNT + 1
          where ID = #{id}
        </update>
      
        <select id="selectRelated" parameterType="life.usc.study.model.Question" resultMap="BaseResultMap">
          select * from QUESTION where id != #{id} and tag regexp #{tag}
        </select>
      
        <select id="countBySearch" parameterType="life.usc.study.dto.QuestionQueryDTO" resultType="java.lang.Integer">
          select count(*) from QUESTION
          <where>
            <if test="search != null">
              and title regexp #{search}
            </if>
            <if test="topHot != null">
              and tag regexp #{topHot}
            </if>
          </where>
        </select>
      
        <select id="selectBySearch" parameterType="life.usc.study.dto.QuestionQueryDTO" resultMap="BaseResultMap">
          select * from QUESTION
          <where>
            <if test="search != null">
              and title regexp #{search}
            </if>
            <if test="topHot != null">
              and tag regexp #{topHot}
            </if>
          </where>
          order by gmt_create desc limit #{offset}, #{size}
        </select>
      </mapper>
      ```

      

      # 第六步 修补topHots内部无序

      因为一开始采取了以下写法 导致输出的并不是有序的

      ```java
      for (HotTagDTO hotTagDTO : pq) {
          topHots.addFirst(hotTagDTO.getName());
      }
      ```
      因此我们将其改为

      ```java
      while(!pq.isEmpty()) {
          topHots.addFirst(pq.poll().getName());
      }
      ```

