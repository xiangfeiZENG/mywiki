# Hibernate 使用Group by自定义查询

Hibernate的优势是ddd设计，支持面向对象，但是有时需要自定义sql，这样更便捷的解决某些特定问题。

repository自定义查询方法的方式：

```java
public interface SettleCdrGprsRepository extends JpaRepository<SettleCdrGprs, String> {

    @Query("SELECT gprs.settleItem,sum(gprs.uploadVol) from SettleCdrGprs gprs where gprs.invoicingPeriod = ?1 group by gprs.settleItem")
    List<Object[]> findUploadVolGroupBySettleItem(String invoicingPeriod);
}
```

调用解析：

```java
List<Object[]> settleItemUploadVolList = settleCdrGprsRepository.findUploadVolGroupBySettleItem(invoicingPeriod);

for(Object[] rowArray : settleItemUploadVolList) {
    for(int i=0; i<rowArray.length; i++) {
        log.error(String.valueOf(rowArray[i]) + ",");
    }
}
```



通过vo方式，直接解析为vo对象：

定义vo对象

```java
@Data
public class SettleItemUploadVolVo {
    private String settleItem;
    private Long uploadVol;
}
```

修改repository

```java
@Query("SELECT new com.cmit.mvne.billing.settle.vo.SettleItemUploadVolVo(gprs.settleItem, sum(gprs.uploadVol)) from SettleCdrGprs gprs where gprs.invoicingPeriod = ?1 group by gprs.settleItem")
List<SettleItemUploadVolVo> findUploadVolGroupBySettleItem(String invoicingPeriod);
```

