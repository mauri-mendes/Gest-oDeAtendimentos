Mauricio Mendes (mauriciobrisolla04@gmail.com)

Cliente:

Uma parte da interface será destinada ao cliente.
Ao chegar, o cliente deve registrar sua presença clicando em um botão, e o sistema automaticamente atribuirá a ele um número de atendimento sequencial.
Atendente:

Outra parte da interface será o painel do atendente, que exibirá a fila de clientes aguardando com seus respectivos números.
O atendente poderá clicar em um botão para chamar o próximo cliente, removendo-o da fila e exibindo seu número como atendido.
Requisitos da Aplicação:

Implementar a fila utilizando uma estrutura de dados Queue.
A interface gráfica deve ser clara e separada em duas partes: cliente e atendente.
Ao chamar um cliente, o painel do atendente deve exibir o número do cliente chamado.
Após o atendimento, o cliente deve ser removido da fila.
A aplicação deve exibir mensagens indicando se a fila está vazia ou se ainda há clientes aguardando.
O layout deve ser simples, mas funcional, permitindo interatividade entre as duas partes. 
A numeração dos clientes deve ser automática e sequencial, começando do número 1.
Exemplo de Funcionamento:

Quando um cliente registra sua presença, ele recebe o número 1, o próximo cliente recebe o número 2, e assim por diante.
O atendente visualizará o número do próximo cliente na fila e poderá clicar para chamá-lo, o que removerá o cliente da lista e o marcará como atendido.

Implemente a funcionalidade que permita priorizar o atendimento de alguns clientes com base em uma determinada regra (por exemplo, idosos, gestantes, PCD, atendimento geral).

import javax.swing.*;
import java.awt.*;
import java.time.Duration;
import java.time.LocalDateTime;
import java.util.PriorityQueue;

/**
 * Simulador de Fila de Atendimento com Prioridade Inteligente
 * Interface gráfica com painel de cliente e atendente.
 * Utiliza PriorityQueue para gerenciar a fila com base em tipo de atendimento e tempo de espera.
 *
 * @author Mauricio
 * @version 2.0
 */
public class FilaAtendimentoGUI extends JFrame {
    private final PriorityQueue<Cliente> fila;
    private int contador = 1;

    // Componentes do cliente
    private final JComboBox<String> tipoCombo;
    private final JLabel numeroClienteLabel;

    // Componentes do atendente
    private final DefaultListModel<String> listaModel;
    private final JLabel atendidoLabel;

    /**
     * Construtor da interface gráfica.
     */
    public FilaAtendimentoGUI() {
        super("Fila de Atendimento");
        fila = new PriorityQueue<>();

        setLayout(new GridLayout(1, 2));
        setSize(700, 350);
        setDefaultCloseOperation(EXIT_ON_CLOSE);

        // Painel do cliente
        JPanel clientePanel = new JPanel(new GridLayout(5, 1));
        clientePanel.setBorder(BorderFactory.createTitledBorder("Cliente"));

        tipoCombo = new JComboBox<>(new String[]{"Geral", "Idoso", "Gestante", "PCD"});
        JButton registrarButton = new JButton("Registrar Presença");
        numeroClienteLabel = new JLabel("Número atribuído: ");

        registrarButton.addActionListener(e -> {
            String tipo = (String) tipoCombo.getSelectedItem();
            Cliente cliente = new Cliente(contador++, tipo, LocalDateTime.now());
            fila.add(cliente);
            numeroClienteLabel.setText("Número atribuído: " + cliente.numero());
            atualizarLista();
        });

        clientePanel.add(new JLabel("Tipo de atendimento:"));
        clientePanel.add(tipoCombo);
        clientePanel.add(registrarButton);
        clientePanel.add(numeroClienteLabel);

        // Painel do atendente
        JPanel atendentePanel = new JPanel(new BorderLayout());
        atendentePanel.setBorder(BorderFactory.createTitledBorder("Atendente"));

        listaModel = new DefaultListModel<>();
        JList<String> listaFila = new JList<>(listaModel);
        JScrollPane scrollPane = new JScrollPane(listaFila);

        JButton chamarButton = new JButton("Chamar Próximo");
        atendidoLabel = new JLabel("Cliente atendido: ");

        chamarButton.addActionListener(e -> {
            if (fila.isEmpty()) {
                atendidoLabel.setText("Fila vazia.");
            } else {
                Cliente cliente = fila.poll();
                atendidoLabel.setText("Cliente atendido: #" + cliente.numero() + " (" + cliente.tipo() + ")");
                atualizarLista();
            }
        });

        atendentePanel.add(scrollPane, BorderLayout.CENTER);
        atendentePanel.add(chamarButton, BorderLayout.SOUTH);
        atendentePanel.add(atendidoLabel, BorderLayout.NORTH);

        add(clientePanel);
        add(atendentePanel);

        setVisible(true);
    }

    /**
     * Atualiza a lista de clientes exibida no painel do atendente.
     */
    private void atualizarLista() {
        listaModel.clear();
        for (Cliente c : fila) {
            listaModel.addElement("Cliente #" + c.numero() + " (" + c.tipo() + ") - Prioridade: " + c.prioridadeTotal());
        }
    }

    /**
     * Record que representa um cliente com prioridade dinâmica.
     * Implementa Comparable para ordenação na fila.
     */
    public record Cliente(int numero, String tipo, LocalDateTime chegada) implements Comparable<Cliente> {
        @Override
        public int compareTo(Cliente outro) {
            return Integer.compare(outro.prioridadeTotal(), this.prioridadeTotal());
        }

        public int prioridadeTotal() {
            return prioridadeBase() + bonusPorEspera();
        }

        private int prioridadeBase() {
            return switch (tipo) {
                case "PCD" -> 100;
                case "Gestante" -> 90;
                case "Idoso" -> 80;
                default -> 50;
            };
        }

        private int bonusPorEspera() {
            return (int) Duration.between(chegada, LocalDateTime.now()).toMinutes();
        }
    }

    /**
     * Método principal para iniciar a aplicação.
     * @param args argumentos da linha de comando
     */
    public static void main(String[] args) {
        SwingUtilities.invokeLater(FilaAtendimentoGUI::new);
    }
}


<img width="693" height="343" alt="image" src="https://github.com/user-attachments/assets/d1ea3c06-9b39-4ffd-a4ad-6b9d0dc6e683" />

<img width="694" height="343" alt="image" src="https://github.com/user-attachments/assets/ca3eb080-690b-409c-b060-cfb33accebf4" />

