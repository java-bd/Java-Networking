### Java Program to Check a Server Status and Generate an Alarm if The Server is Not Available

* #### java -jar ServerStatus.jar D:\alarm.wav

```java
/*
ServerList.txt
http://192.168.241.12
http://192.168.241.246
 */


import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.LinkedList;
import java.util.Timer;
import java.util.TimerTask;
import java.util.logging.Level;
import java.util.logging.Logger;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;

class ServerStatusCheck {

    String serverIP;
    String mediaFile;
    public static int iter = 1;

    public ServerStatusCheck(LinkedList<String> serverList, String mediaFile) throws Exception {

        for (String serverIP : serverList) {
            try {
                URL url = new URL(serverIP);
                HttpURLConnection connection = (HttpURLConnection) url.openConnection();
                connection.setRequestMethod("GET");
                connection.connect();

                String serverStatus;
                serverStatus = Integer.toString(connection.getResponseCode());

                if ("200".equals(serverStatus)) {

                } else {
                    System.out.println(serverIP + " Server is NOOOOOOOOOOOOT OK");
                    new ProcessBuilder("C:\\Program Files\\Windows Media Player\\wmplayer.exe", mediaFile).start();
                }
            } catch (IOException e) {
                System.out.println(serverIP + " Server is NOOOOOOOOOOOOT OK For Connectivity Problem");
                new ProcessBuilder("C:\\Program Files\\Windows Media Player\\wmplayer.exe", mediaFile).start();
            }

        }
        if (iter % 5 == 0) {
            DateFormat sdf = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss");
            Date date = new Date();
            System.out.print(sdf.format(date) + "\n");
        } else {
            System.out.print("-");
        }
        iter++;
    }
}

public class ServerStatus {

    public static void main(String[] args) throws Exception {

        System.out.println("======================================================================================");
        System.out.println("                        Server Status Checkup Program.                                ");
        System.out.println("          Dead Servers or Link Missing Servers will be shown Here.                     ");
        System.out.println("======================================================================================");

        LinkedList<String> serverList = new LinkedList<>();
        String mediaFile = args[0];

        try (BufferedReader br = new BufferedReader(new FileReader("ServerList.txt"))) {
            String line;
            while ((line = br.readLine()) != null) {
                serverList.add(line);
            }
        }

        Timer timer = new Timer();
        timer.scheduleAtFixedRate(new TimerTask() {
            @Override
            public void run() {
                try {
                    ServerStatusCheck serverStatusCheck = new ServerStatusCheck(serverList, mediaFile);
                } catch (Exception ex) {
                    Logger.getLogger(ServerStatus.class.getName()).log(Level.SEVERE, null, ex);
                }
            }
        }, 0, 2000);
    }
}


```
