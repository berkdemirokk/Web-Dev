# Web-Dev
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpRequest.BodyPublishers;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.util.Map;

public class AOMService {

    private static final String BASE_URL = "https://api.aom.com";
    private final ObjectMapper objectMapper;

    public AOMService() {
        this.httpClient = HttpClient.newHttpClient();
        this.objectMapper = new ObjectMapper();
    }

    public String createSubscriber(Map<String, String> subscriberDetails) throws Exception {
        URI uri = new URI(BASE_URL + "/createSubscriber");
        String requestBody = objectMapper.writeValueAsString(subscriberDetails);

        HttpRequest request = HttpRequest.newBuilder()
                .uri(uri)
                .header("Content-Type", "application/json")
                .POST(BodyPublishers.ofString(requestBody))
                .build();

        HttpResponse<String> response = httpClient.send(request, HttpResponse.BodyHandlers.ofString());
        
        if (response.statusCode() == 201) {
            return response.body(); // Assume response contains created subscriber details
        } else {
            throw new RuntimeException("Failed to create subscriber: " + response.body());
        }
    }
}
