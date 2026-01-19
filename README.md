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

## PART 2 — OBJECT SERIALIZATION
### Product Class
```java

import java.io.Serializable;

public class Product implements Serializable {
    private long id;
    private String name;
    private String brand;
    private double price;
    private String description;
    private int stock;

    public Product(long id, String name, String brand, double price, String description, int stock) {
        this.id = id;
        this.name = name;
        this.brand = brand;
        this.price = price;
        this.description = description;
        this.stock = stock;
    }

    public long getId() {
        return id;
    }

    @Override
    public String toString() {
        return "Product{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", brand='" + brand + '\'' +
                ", price=" + price +
                ", stock=" + stock +
                '}';
    }
}

```

### IProduitMetier Interface

```java

import java.util.List;

public interface IProduitMetier {
    void add(Product p);
    List<Product> getAll();
    Product findById(long id);
    void delete(long id);
    void saveAll();
}

```

### MetierProduitImpl Class
```java
import java.io.*;
import java.util.ArrayList;
import java.util.List;

public class MetierProduitImpl implements IProduitMetier {

    private List<Product> products = new ArrayList<>();
    private String fileName = "products.dat";

    @Override
    public void add(Product p) {
        products.add(p);
    }

    @Override
    public List<Product> getAll() {
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(fileName))) {
            products = (List<Product>) ois.readObject();
        } catch (Exception e) {
            products = new ArrayList<>();
        }
        return products;
    }

    @Override
    public Product findById(long id) {
        for (Product p : products) {
            if (p.getId() == id) return p;
        }
        return null;
    }

    @Override
    public void delete(long id) {
        products.removeIf(p -> p.getId() == id);
    }

    @Override
    public void saveAll() {
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(fileName))) {
            oos.writeObject(products);
            System.out.println("Products saved successfully.");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
## Application Class (Menu)
```java
import java.util.Scanner;

public class Application {

    public static void main(String[] args) {
        MetierProduitImpl metier = new MetierProduitImpl();
        Scanner scanner = new Scanner(System.in);

        metier.getAll();

        int choice;
        do {
            System.out.println("\n1. Display products");
            System.out.println("2. Search product by ID");
            System.out.println("3. Add product");
            System.out.println("4. Delete product");
            System.out.println("5. Save products");
            System.out.println("6. Exit");
            System.out.print("Choice: ");

            choice = scanner.nextInt();

            switch (choice) {
                case 1:
                    metier.getAll().forEach(System.out::println);
                    break;

                case 2:
                    System.out.print("Enter ID: ");
                    long id = scanner.nextLong();
                    System.out.println(metier.findById(id));
                    break;

                case 3:
                    System.out.print("ID: ");
                    long pid = scanner.nextLong();
                    scanner.nextLine();
                    System.out.print("Name: ");
                    String name = scanner.nextLine();
                    System.out.print("Brand: ");
                    String brand = scanner.nextLine();
                    System.out.print("Price: ");
                    double price = scanner.nextDouble();
                    System.out.print("Stock: ");
                    int stock = scanner.nextInt();

                    metier.add(new Product(pid, name, brand, price, "", stock));
                    break;

                case 4:
                    System.out.print("Enter ID to delete: ");
                    metier.delete(scanner.nextLong());
                    break;

                case 5:
                    metier.saveAll();
                    break;
            }
        } while (choice != 6);
    }
}
```
## PART 3 — EXCEPTION HANDLING
### Exercise 1: Calculator
```java
public class Calculator {

    public static double divide(double a, double b) {
        if (b == 0) {
            System.out.println("Error: Division by zero not possible.");
            return 0;
        }
        return a / b;
    }

    public static int convertToNumber(String text) {
        try {
            return Integer.parseInt(text);
        } catch (NumberFormatException e) {
            System.out.println("Error: '" + text + "' is not a valid number");
            return 0;
        }
    }

    public static double calculate(char op, double a, double b) {
        switch (op) {
            case '+': return a + b;
            case '-': return a - b;
            case '*': return a * b;
            case '/': return divide(a, b);
            default:
                System.out.println("Error: Operation '" + op + "' not supported");
                return 0;
        }
    }
}
```
## PART 4 — CUSTOM EXCEPTIONS

### Exercise 2 TooFastException
```java
public class TooFastException extends Exception {
    public TooFastException(int speed) {
        super("This is an exception of type TooFastException. Speed involved: " + speed);
    }
}
```

### Vehicle Class
```java
public class Vehicle {

    public Vehicle() {}

    public void testSpeed(int speed) throws TooFastException {
        if (speed > 90) {
            throw new TooFastException(speed);
        }
        System.out.println("Speed is safe: " + speed);
    }

    public static void main(String[] args) {
        Vehicle v = new Vehicle();

        try {
            v.testSpeed(70);
            v.testSpeed(120);
        } catch (TooFastException e) {
            e.printStackTrace();
        }
    }
}
```
