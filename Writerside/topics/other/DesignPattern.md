# 设计模式

## Example

### ex1

##### 接口类，定义

```java
public interface DeductInvalidService {
    void handleInvalid(JSONArray items);
    void updateBudget(List<String> businessNos, String businessType);
}
```

##### 抽象类，实现部分共有方法

```java
public abstract class AbstractDeductInvalidService implements DeductInvalidService {
    @Override
    public void updateBudget(List<String> businessNos, String businessType) {
        //logic code
    }
}
```

##### 具体实现类 若干

```java
@Service("generateInvalidService")
public class GenerateInvalidService extends AbstractDeductInvalidService {
    @Override
    public void handleInvalid(JSONArray items) {
        //logic code
        super.updateBudget(nos, businessType);
    }
}
```

##### 使用

```java
@Component
public class DeductInvalidFactory {
    @Autowired
    private ApplicationContext context;
    private static Map<String, String> map = new HashMap<>();
    static {
        map.put("businessType1", "generateInvalidService");
        map.put("businessType2", "generateInvalidService2");
        map.put("businessType3", "generateInvalidService3");
       //etc.
    }
    public DeductInvalidService getInvalidHandler(String businessType) {
        return (DeductInvalidService) context.getBean(map.get(businessType));
    }
}

//using in code
public class UsingExample {
    public static void main(String[] args) {
        DeductInvalidService service = deductInvalidFactory.getInvalidHandler("businessType");
        service.handleInvalid(items);
    } 
}
```

