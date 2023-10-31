# -4.-
Урок 4. Обработка исключений

1. Создать программу управления банковским счетом (Account).

Программа должна позволять пользователю вводить начальный баланс счета, сумму депозита и сумму снятия средств. При этом она должна обрабатывать следующие исключительные ситуации:

Попытка создать счет с отрицательным начальным балансом должна вызывать исключение IllegalArgumentException с соответствующим сообщением.
Попытка внести депозит с отрицательной суммой должна вызывать исключение IllegalArgumentException с соответствующим сообщением.
Попытка снять средства, сумма которых превышает текущий баланс, должна вызывать исключение InsufficientFundsException с сообщением о недостаточных средствах и текущим балансом.

Продемонстрируйте работу вашего приложения:
Программа должна обрабатывать все исключения с помощью конструкции try-catch, выводя соответствующие сообщения об ошибках.


public class Account {
    private double balance;

    public Account(double initialBalance) {
        if (initialBalance < 0) {
            throw new IllegalArgumentException("Начальный баланс не может быть отрицательным");
        }
        this.balance = initialBalance;
    }

    public void deposit(double amount) {
        if (amount < 0) {
            throw new IllegalArgumentException("Сумма депозита не может быть отрицательной");
        }
        balance += amount;
    }

    public void withdraw(double amount) throws InsufficientFundsException {
        if (amount > balance) {
            throw new InsufficientFundsException("Недостаточно средств", balance);
        }
        balance -= amount;
    }

    public double getBalance() {
        return balance;
    }
}

class InsufficientFundsException extends Exception {
    private double currentBalance;

    public InsufficientFundsException(String message, double currentBalance) {
        super(message);
        this.currentBalance = currentBalance;
    }

    public double getCurrentBalance() {
        return currentBalance;
    }
}


public class Main {
    public static void main(String[] args) {
        try {
            Account account = new Account(100.0);
            System.out.println("Начальный баланс: " + account.getBalance());

            account.deposit(50.0);
            System.out.println("Баланс после внесения депозита: " + account.getBalance());

            account.withdraw(30.0);
            System.out.println("Баланс после снятия средств: " + account.getBalance());

            account.withdraw(200.0); // вызовет исключение InsufficientFundsException
        } catch (IllegalArgumentException e) {
            System.out.println("Ошибка: " + e.getMessage());
        } catch (InsufficientFundsException e) {
            System.out.println("Ошибка: " + e.getMessage() + ", текущий баланс: " + e.getCurrentBalance());
        }
    }
}



2. Создать несколько типов счетов, унаследованных от Account, например: CreditAcciunt, DebitAccount.
Создать класс (Transaction), позволяющий проводить транзакции между счетами (переводить сумму с одного счета на другой)

Класс Transaction должен возбуждать исключение в случае неудачной попытки перевести деньги с одного счета на другой.

Продемонстрируйте работу вашего приложения:
Программа должна обрабатывать все исключения с помощью конструкции try-catch, выводя соответствующие сообщения об ошибках.



public class Account {
    protected double balance;

    public Account(double initialBalance) {
        if (initialBalance < 0) {
            throw new IllegalArgumentException("Начальный баланс не может быть отрицательным");
        }
        this.balance = initialBalance;
    }

    public double getBalance() {
        return balance;
    }

    public void deposit(double amount) {
        if (amount < 0) {
            throw new IllegalArgumentException("Сумма депозита не может быть отрицательной");
        }
        balance += amount;
    }

    public void withdraw(double amount) throws InsufficientFundsException {
        if (amount > balance) {
            throw new InsufficientFundsException("Недостаточно средств", balance);
        }
        balance -= amount;
    }
}

class CreditAccount extends Account {
    private double creditLimit;

    public CreditAccount(double initialBalance, double creditLimit) {
        super(initialBalance);
        this.creditLimit = creditLimit;
    }

    public double getCreditLimit() {
        return creditLimit;
    }

    @Override
    public void withdraw(double amount) throws InsufficientFundsException {
        if (amount > balance + creditLimit) {
            throw new InsufficientFundsException("Недостаточно средств и доступного кредитного лимита", balance);
        }
        balance -= amount;
    }
}

class DebitAccount extends Account {
    public DebitAccount(double initialBalance) {
        super(initialBalance);
    }
}

class InsufficientFundsException extends Exception {
    private double currentBalance;

    public InsufficientFundsException(String message, double currentBalance) {
        super(message);
        this.currentBalance = currentBalance;
    }

    public double getCurrentBalance() {
        return currentBalance;
    }
}

public class Transaction {
    public static void transfer(Account source, Account destination, double amount) throws InsufficientFundsException {
        source.withdraw(amount);
        destination.deposit(amount);
    }
}
