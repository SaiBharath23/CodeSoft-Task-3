import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class CurrencyConverter {

    private static final String API_KEY = "YOUR_API_KEY";
    private static final String API_URL = "https://open.er-api.com/v6/latest/";

    public static void main(String[] args) {
        try {
            String baseCurrency = getUserInput("Enter the base currency code: ");
            String targetCurrency = getUserInput("Enter the target currency code: ");
            double amount = Double.parseDouble(getUserInput("Enter the amount to convert: "));

            double exchangeRate = getExchangeRate(baseCurrency, targetCurrency);
            if (exchangeRate != -1) {
                double convertedAmount = convertCurrency(amount, exchangeRate);
                System.out.printf("%.2f %s is equal to %.2f %s%n", amount, baseCurrency, convertedAmount, targetCurrency);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private static String getUserInput(String prompt) throws IOException {
        System.out.print(prompt);
        BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
        return reader.readLine();
    }

    private static double getExchangeRate(String baseCurrency, String targetCurrency) throws IOException {
        String apiUrl = API_URL + baseCurrency + "?apikey=" + API_KEY;

        URL url = new URL(apiUrl);
        HttpURLConnection connection = (HttpURLConnection) url.openConnection();

        if (connection.getResponseCode() == 200) {
            BufferedReader reader = new BufferedReader(new InputStreamReader(connection.getInputStream()));
            StringBuilder response = new StringBuilder();
            String line;

            while ((line = reader.readLine()) != null) {
                response.append(line);
            }

            reader.close();

            // Parse JSON response to get exchange rate
            String jsonResponse = response.toString();
            double rate = parseExchangeRate(jsonResponse, targetCurrency);

            if (rate != -1) {
                return rate;
            } else {
                System.out.println("Error: Invalid target currency code.");
                return -1;
            }
        } else {
            System.out.println("Error: Unable to fetch exchange rates. Status code: " + connection.getResponseCode());
            return -1;
        }
    }

    private static double parseExchangeRate(String jsonResponse, String targetCurrency) {
        // Implement your JSON parsing logic here based on the response format of the API
        // For simplicity, let's assume the response is a JSON object with a "rates" field
        // containing key-value pairs of currency codes and exchange rates.

        // Example:
        // {"rates": {"USD": 1.2, "EUR": 0.8, ...}}

        // You should modify this part based on the actual API response format.
        // For a more robust solution, consider using a JSON parsing library like Jackson or Gson.

        // Replace this logic with your actual parsing code
        return 1.0; // Placeholder value, replace with the actual exchange rate
    }

    private static double convertCurrency(double amount, double exchangeRate) {
        return amount * exchangeRate;
    }
}
