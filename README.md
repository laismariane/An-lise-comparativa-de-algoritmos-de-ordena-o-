import java.io.*;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.*;
import javax.swing.JFileChooser;

public class Main {

    public static void main(String[] args) {
        // Selecionar o arquivo tweets.csv
        JFileChooser fileChooser = new JFileChooser();
        fileChooser.setDialogTitle("Selecione o arquivo tweets.csv");
        int userSelection = fileChooser.showOpenDialog(null);
        if (userSelection != JFileChooser.APPROVE_OPTION) {
            System.out.println("Nenhum arquivo selecionado. Encerrando o programa.");
            return;
        }
        File file = fileChooser.getSelectedFile();
        String filePath = file.getAbsolutePath();

        // Transformações
        try {
            // Transformação 1: Formatação das datas
            BufferedReader reader = new BufferedReader(new FileReader(filePath));
            BufferedWriter writer = new BufferedWriter(new FileWriter("D:/tweets_formated_data.csv"));
            String line;
            while ((line = reader.readLine()) != null) {
                String[] parts = line.split(",");
                if (parts.length <= 2) {
                    System.err.println("Erro: Linha inválida, pulando para a próxima linha.");
                    continue;
                }
                String dateString = parts[2]; // Assuming date is at index 2
                String text = parts[5]; // Assuming text is at index 5
                SimpleDateFormat inputFormat = new SimpleDateFormat("EEE MMM dd HH:mm:ss zzz yyyy", Locale.ENGLISH);
                Date date;
                try {
                    date = inputFormat.parse(dateString);
                } catch (ParseException e) {
                    System.err.println("Erro ao analisar a data na linha: " + line);
                    continue;
                }
                SimpleDateFormat outputFormat = new SimpleDateFormat("dd/MM/yyyy");
                String formattedDate = outputFormat.format(date);
                writer.write(formattedDate + "," + text + "\n");
            }
            reader.close();
            writer.close();
            System.out.println("Transformação 1 concluída. Arquivo 'tweets_formated_data.csv' gerado com sucesso.");

            // Transformação 2: Adição das colunas "mentioned_person" e "mentioned_person_count"
            reader = new BufferedReader(new FileReader("D:/tweets_formated_data.csv"));
            writer = new BufferedWriter(new FileWriter("D:/tweets_mentioned_persons.csv"));
            while ((line = reader.readLine()) != null) {
                String[] parts = line.split(",");
                String text = parts[1]; // Assuming text is at index 1
                String mentionedPerson = "";
                int mentionedPersonCount = 0;
                String[] words = text.split("\\s+");
                for (String word : words) {
                    if (word.startsWith("@")) {
                        mentionedPerson += word.substring(1) + "/";
                        mentionedPersonCount++;
                    }
                }
                if (!mentionedPerson.isEmpty()) {
                    mentionedPerson = mentionedPerson.substring(0, mentionedPerson.length() - 1);
                }
                writer.write(line + "," + (mentionedPerson.isEmpty() ? "null" : mentionedPerson) + "," + mentionedPersonCount + "\n");
            }
            reader.close();
            writer.close();
            System.out.println("Transformação 2 concluída. Arquivo 'tweets_mentioned_persons.csv' gerado com sucesso.");
        } catch (IOException e) {
            e.printStackTrace();
        }

        // Ordenações
        try {
            // Ordenação 1: Ordenar o arquivo pela data dos tweets em ordem crescente
            ordenarPorData("D:/tweets_mentioned_persons.csv");

            // Ordenação 2: Ordenar o arquivo pelo número de pessoas mencionadas no tweet em ordem decrescente
            ordenarPorMencoes("D:/tweets_mentioned_persons.csv");

            // Ordenação 3: Ordenar o arquivo em ordem alfabética pelo nome dos usuários
            ordenarPorUsuarios("D:/tweets_mentioned_persons.csv");
        } catch (IOException e) {
            e.printStackTrace();
        }

        System.out.println("Todas as operações foram concluídas.");
    }

    // Função auxiliar para comparar as datas
    private static int compareDates(String line1, String line2) {
        String[] parts1 = line1.split(",");
        String[] parts2 = line2.split(",");
        String dateString1 = parts1[0];
        String dateString2 = parts2[0];
        SimpleDateFormat format = new SimpleDateFormat("dd/MM/yyyy");
        try {
            Date date1 = format.parse(dateString1);
            Date date2 = format.parse(dateString2);
            return date1.compareTo(date2);
        } catch (ParseException e) {
            e.printStackTrace();
        }
        return 0;
    }

    // Ordenar o arquivo pela data dos tweets em ordem crescente
    private static void ordenarPorData(String filePath) throws IOException {
        String[] algoritmos = {"InsertionSort"};
        String caso = "medioCaso";

        for (String algoritmo : algoritmos) {
            String outputFileName = "tweets_mentioned_persons_date_" + algoritmo + "_" + caso + ".csv";
            System.out.println("Ordenando por data com " + algoritmo + " (" + caso + ")...");
            List<String> lines = lerArquivo(filePath);
            // Implementar a ordenação usando o algoritmo especificado
            insertionSort(lines, "date"); // Exemplo de uso do Insertion Sort
            gerarArquivoOrdenado(lines, outputFileName);
        }
    }

    // Ordenar o arquivo pelo número de pessoas mencionadas no tweet em ordem decrescente
    private static void ordenarPorMencoes(String filePath) throws IOException {
        String[] algoritmos = {"InsertionSort"};
        String caso = "medioCaso";

        for (String algoritmo : algoritmos) {
            String outputFileName = "tweets_mentioned_persons_count_" + algoritmo + "_" + caso + ".csv";
            System.out.println("Ordenando por menções com " + algoritmo + " (" + caso + ")...");
            List<String> lines = lerArquivo(filePath);
            // Implementar a ordenação usando o algoritmo especificado
            // insertionSort(lines, "mentioned_person_count"); // Exemplo de uso do Insertion Sort
            gerarArquivoOrdenado(lines, outputFileName);
        }
    }

    // Ordenar o arquivo em ordem alfabética pelo nome dos usuários
    private static void ordenarPorUsuarios(String filePath) throws IOException {
        String[] algoritmos = {"InsertionSort"};
        String caso = "medioCaso";

        for (String algoritmo : algoritmos) {
            String outputFileName = "tweets_mentioned_persons_user_" + algoritmo + "_" + caso + ".csv";
            System.out.println("Ordenando por usuários com " + algoritmo + " (" + caso + ")...");
            List<String> lines = lerArquivo(filePath);
            // Implementar a ordenação usando o algoritmo especificado
            // insertionSort(lines, "user"); // Exemplo de uso do Insertion Sort
            gerarArquivoOrdenado(lines, outputFileName);
        }
    }

    // Implementação do Insertion Sort
    private static void insertionSort(List<String> lines, String sortBy) {
        // Implementar a lógica de ordenação usando o Insertion Sort
        // Este método deve ordenar a lista 'lines' com base no atributo especificado em 'sortBy'
    }

    // Função auxiliar para ler o arquivo e retornar as linhas como uma lista
    private static List<String> lerArquivo(String filePath) throws IOException {
        List<String> lines = new ArrayList<>();
        BufferedReader reader = new BufferedReader(new FileReader(filePath));
        String line;
        while ((line = reader.readLine()) != null) {
            lines.add(line);
        }
        reader.close();
        return lines;
    }

    // Função auxiliar para gerar o arquivo ordenado
    private static void gerarArquivoOrdenado(List<String> lines, String outputFileName) throws IOException {
        BufferedWriter writer = new BufferedWriter(new FileWriter("D:/" + outputFileName));
        for (String sortedLine : lines) {
            writer.write(sortedLine + "\n");
        }
        writer.close();
        System.out.println("Arquivo '" + outputFileName + "' gerado com sucesso.");
    }
}
