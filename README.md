# Conta Thread - Trabalho prático sobre Threads


Este trabalho segue a referência do link [Curso Java e Orientação a Objetos › Apostila › Capítulo 20 - Apêndice - Problemas com concorrência](https://www.caelum.com.br/apostila-java-orientacao-objetos/apendice-problemas-com-concorrencia/)

Ações práticas do trabalho:

## 1ª Parte

A proposta é criar uma situação hipotética com um conjunto de classes que simulam um ambiente bancário, e em uma das contas, há uma tentativa de realizar diversos saques simultâneos. A ideia que a conta não fique negativada.

1. Criar um projeto Java no **NetBeans** chamado ``ContaThread``;
2. Criar a classe de nome ``Conta`` com o seguinte conteúdo:

```java
public class Conta {

    private double saldo;

    public double getSaldo() {
        return saldo;
    }

    public void sacar(double valor) {
        double saldoAtualizado = this.saldo - valor;
        this.saldo = saldoAtualizado;
    }

    public void depositar(double valor) {
        double novoSaldo = this.saldo + valor;
        this.saldo = novoSaldo;
    }
}
```

3. Criar a classe de nome ``ProcessadorSaques`` com o seguinte conteúdo:

```java
public class ProcessadorSaques implements Runnable {

    private final Conta conta;
    private final int quantidadeSaques;
    private final double valorSaque;

    public ProcessadorSaques(Conta conta, int quantidadeSaques, double valorSaque) {
        this.conta = conta;
        this.quantidadeSaques = quantidadeSaques;
        this.valorSaque = valorSaque;
    }
    
    @Override
    public void run() {
        for(int i = 1; i < quantidadeSaques; i++) {
            realizaSaque(valorSaque);
            System.out.println(Thread.currentThread().getName() + "- " + conta.getSaldo());
        }
    }
    
    private void realizaSaque(double valorSaque) {
        if (conta.getSaldo() >= valorSaque) {
            atrasaUmPouquinho();
            conta.sacar(valorSaque);
        }
    }

    private void atrasaUmPouquinho() {
//        try {
//            Thread.sleep(10);
//        } catch (InterruptedException ex) {
//        }
    }
}

```

4. Criar a classe de nome ``Processo`` com o seguinte conteúdo:
```java
public class Processo {
    
    public static void main(String[] args) throws InterruptedException {
        final int TOTAL_THREADS = 10;
        Conta conta = new Conta();
        conta.depositar(1000);
        ProcessadorSaques processadorSaques = new ProcessadorSaques(conta, 20, 50);
        
        Thread [] todas = new Thread[TOTAL_THREADS];
        
        for(int i = 0; i < TOTAL_THREADS; i++) {
            todas[i] = new Thread(processadorSaques, "thread" + i);
        }

        for(int i = 0; i < TOTAL_THREADS; i++) {
            todas[i].start();
        }        
        
        for(int i = 0; i < TOTAL_THREADS; i++) {
            todas[i].join();
        }
        
        System.out.println("Saldo Final = " + conta.getSaldo());
    }
}

```

5. Execute e observe o comportamento e resultado

## 2ª Parte

A proposta é ampliar a situação hipotética anterior embutindo um "atraso" na realização dos saques. A ideia aproximar o ambiente de uma situação mais realistica, mas mantendo a conta não negativada.

1. Descomente o trecho de código comentado na classe ```ProcessadorSaques``` no método  ```atrasaUmPouquinho``` 
2. Execute e observe o comportamento e resultado

## 3ª Parte

A proposta corrigir o problema da situação anterior embutindo incluindo um sincronismo de threads. A ideia resolver o problema apresentado na situação mais realistica que envolva concorrência, agora sim, garantindo a conta não ficar negativada.

1. Inclua a palavra reservada ```synchronized``` no método ```realizaSaque``` da classe ```ProcessadorSaques```  
2. Execute e observe o comportamento e resultado

### Bom Trabalho