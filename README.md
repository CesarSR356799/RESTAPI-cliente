# RESTAPI-cliente
Desarrolle un cliente para una REST API publica.
package consulta_ip;

/**
 *
 * @author Julio Cesar Saldaña Ramirez
 * 
 */
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.net.HttpURLConnection;
import java.net.URL;
import java.io.*;
import javax.imageio.ImageIO;
import java.awt.image.BufferedImage;

public class Consulta_ip {
    public static void main(String[] args) {
        // Crear una instancia de la clase GUI
        SwingUtilities.invokeLater(new Runnable() {
            public void run() {
                interfaz_y_busqueda.createAndShowGUI();
            }
        });
    }
}
//una pagina de api la cual si puede ser visualizada la imagen es la pag:https://rickandmortyapi.com/
//oh tambien esta pagina https://unsplash.com/es/s/fotos/api
//y solo basta con copiar la direccion de la imagen
class interfaz_y_busqueda {
    public static void createAndShowGUI() {
        // Crear la ventana principal
        JFrame frame = new JFrame("Consulta API");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.getContentPane().setBackground(new Color(240, 240, 240)); // Color de fondo

        // Crear un campo de texto para la URL de la API
        JTextField urlField = new JTextField(50);
        JTextField texto_1 = new JTextField("Ingrese el API que desea buscar:");
        urlField.setBackground(new Color(144, 238, 144)); // Color verde claro
        urlField.setBorder(BorderFactory.createLineBorder(Color.BLACK)); // Borde negro

        // Crear un área de texto para mostrar la respuesta de la API
        JTextArea textArea = new JTextArea(50, 50);
        textArea.setEditable(false);
        JScrollPane scrollPane = new JScrollPane(textArea);

        // Crear un panel para organizar los componentes
        JPanel panel = new JPanel();
        panel.setLayout(null);
        panel.setBackground(new Color(240, 240, 240)); // Color de fondo del panel
        urlField.setBounds(185, 5, 450, 30);
        texto_1.setBounds(5, 5, 180, 30);
        scrollPane.setBounds(5, 40, 630, 400); // Posición y tamaño del área de desplazamiento
        panel.add(texto_1);
        panel.add(urlField);
        panel.add(scrollPane);

        // Agregar el panel a la ventana
        frame.getContentPane().add(panel, BorderLayout.CENTER);

        // Agregar un botón para realizar la consulta a la API
        JButton button = new JButton("Consultar API");
        button.setBackground(new Color(30, 144, 255)); // Color de fondo del botón
        button.setForeground(Color.WHITE); // Texto blanco
        button.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent e) {
                // Obtener la URL ingresada por el usuario
                String urlString = urlField.getText();

                // Realizar la consulta a la API
                try {
                    URL url = new URL(urlString);
                    HttpURLConnection conn = (HttpURLConnection) url.openConnection();
                    conn.setRequestMethod("GET");
                    conn.connect();

                    // Verificar si la petición fue correcta
                    int responseCode = conn.getResponseCode();
                    if (responseCode != 200) {
                        throw new RuntimeException("Ocurrió un error: " + responseCode);
                    } else {
                        // Leer la respuesta de la API
                        InputStream inputStream = conn.getInputStream();
                        InputStreamReader inputStreamReader = new InputStreamReader(inputStream);
                        BufferedReader bufferedReader = new BufferedReader(inputStreamReader);
                        String inputLine;
                        StringBuilder response = new StringBuilder();
                        while ((inputLine = bufferedReader.readLine()) != null) {
                            // Agregar cada línea al StringBuilder
                            response.append(inputLine).append("\n");
                        }
                        bufferedReader.close();

                        // Mostrar la respuesta en el área de texto
                        textArea.setText(response.toString());

                        // Mostrar la imagen desde la URL
                        mostrarImagenDesdeURL(urlString);
                    }
                } catch (Exception ex) {
                    ex.printStackTrace();
                    // Mostrar un mensaje de error en el área de texto
                    textArea.setText("Error al consultar la API: " + ex.getMessage());
                }
            }
        });
        button.setBounds(270, 445, 140, 30); // Posición y tamaño del botón
        panel.add(button);
        // Mostrar la ventana
        frame.setSize(650, 510); // Ancho x Alto en píxeles
        frame.setVisible(true);
    }
    private static void mostrarImagenDesdeURL(String urlString) {
        try {
            URL url = new URL(urlString);
            BufferedImage image = ImageIO.read(url);
            if (image != null) {
                ImageIcon icon = new ImageIcon(image);
                JLabel label = new JLabel(icon);
                JOptionPane.showMessageDialog(null, label, "Imagen desde API", JOptionPane.PLAIN_MESSAGE);
            } else {
                JOptionPane.showMessageDialog(null, "No se pudo cargar la imagen", "Error", JOptionPane.ERROR_MESSAGE);
            }
        } catch (IOException ex) {
            ex.printStackTrace();
            JOptionPane.showMessageDialog(null, "Error al cargar la imagen: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
        }
    }
}


