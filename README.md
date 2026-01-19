# java Input/Output

## PART 1 — INPUT / OUTPUT

```java
import java.io.File;
import java.util.Scanner;

public class LsSimulation {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Enter directory path: ");
        String path = scanner.nextLine();

        File directory = new File(path);

        if (!directory.exists() || !directory.isDirectory()) {
            System.out.println("Invalid directory path.");
            return;
        }

        File[] files = directory.listFiles();

        if (files == null) {
            System.out.println("Cannot access directory.");
            return;
        }

        for (File file : files) {
            String type = file.isDirectory() ? "<DIR>" : "<FILE>";

            String permissions = "";
            permissions += file.canRead() ? "r" : "-";
            permissions += file.canWrite() ? "w" : "-";
            permissions += file.isHidden() ? "h" : "-";

            System.out.println(file.getAbsolutePath() + " " + type + " " + permissions);
        }
    }
}
```
