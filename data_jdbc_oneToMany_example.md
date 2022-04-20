```java
public class Article {
    @Id
    private Long id;
    private String author;
    private Set<Comment> comments;

    public Article(String author, Set<Comment> comments) {
        this.author = author;
        this.comments = comments;
    }

    public Long getId() {
        return id;
    }

    public String getAuthor() {
        return author;
    }

    public Set<Comment> getComments() {
        return comments;
    }

    @Override
    public String toString() {
        return "Article{" +
                "id=" + id +
                ", author='" + author + '\'' +
                ", comments=" + comments +
                '}';
    }
  // list를 활용하는 버전
  public class Article {
    @Id
    private Long id;
    private String author;
    // column 네이밍 설정을 위한 어노테이션, 왜인지는 모르겠으나 소문자로했을때 DbActionExecutionException 발생했다.
    @MappedCollection(idColumn = "ARTICLE_ID", keyColumn = "ARTICLE_KEY")
    private List<Comment> comments;
```

```java
@Repository
public interface ArticleRepository extends CrudRepository<Article, Long> {
}
```

```java
public class Comment {
    @Id
    private Long id;
    private String contents;

    public Comment(String contents) {
        this.contents = contents;
    }

    public String getContents() {
        return contents;
    }

    @Override
    public String toString() {
        return "Comment{" +
                "id=" + id +
                ", contents='" + contents + '\'' +
                '}';
    }
```

```java
create table if not exists article
(
    id       bigint primary key auto_increment,
    author   varchar(50)
);

create table if not exists comment
(
    id      bigint primary key auto_increment,
    contents varchar(255),
    article bigint references article(id)
);

//list를 활용하고 article의 의미를 더 명확하게 전달하기 위해서 article_id로 컬럼 네이밍 변경
// list활용을 위한 column article_key
create table if not exists comment
(
    id          bigint primary key auto_increment,
    contents    varchar(255),
    article_id   bigint references article(id),
    article_key int
);
```

```java
@DataJdbcTest
class ArticleRepositoryTest {

    @Autowired
    ArticleRepository articleRepository;

    @Test
    void save_find_Test() {
        List<Comment> comments = List.of(new Comment("123"), new Comment("321"));
        Article ron2 = new Article("ron2", comments);
        Article save = articleRepository.save(ron2);
        Article article = articleRepository.findById(1L).orElseThrow();

        assertThat(article.getComments()).hasSize(2);
        assertThat(article.getComments()).contains(new Comment("123"));
        assertThat(article.getComments()).contains(new Comment("321"));

    }

```

