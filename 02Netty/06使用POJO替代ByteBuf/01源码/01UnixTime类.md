## `UnixTime`类

```java
package org.aidan.pojo;

import java.text.SimpleDateFormat;
import java.util.Date;

/**
 * @author aidan
 */
public class UnixTime {

    private final int value;

    public UnixTime() {
        this((int) (System.currentTimeMillis() / 1000L));
    }

    public UnixTime(int value) {
        this.value = value;
    }

    public long value() {
        return value;
    }

    @Override
    public String toString() {
        return new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date(value * 1000));
    }
}
```

