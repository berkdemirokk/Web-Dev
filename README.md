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
    private final HttpClient httpClient;
    private final ObjectMapper objectMapper;

    public AOMService() {
        this.httpClient = HttpClient.newHttpClient();
        this.objectMapper = new ObjectMapper();
    }

    public String createSubscriber(Map<String, String> subscriberDetails) throws Exception {
        URI uri = new URI(BASE_URL + "/createSubscriber"); // Replace with actual endpoint
        String requestBody = objectMapper.writeValueAsString(subscriberDetails);

        HttpRequest request = HttpRequest.newBuilder()
                .uri(uri)
                .header("Content-Type", "application/json")
                .POST(BodyPublishers.ofString(requestBody))
                .build();

        HttpResponse<String> response = httpClient.send(request, HttpResponse.BodyHandlers.ofString());
        
        if (response.statusCode() == 201) {
            return response.body(); 
            throw new RuntimeException("Failed to create subscriber: " + response.body());
        }
    }
}

public class AOMService {

    // Existing code...

    public String getRemainingBalanceList(String subscriberId) throws Exception {
        URI uri = new URI(BASE_URL + "/subscribers/" + subscriberId + "/balances"); // Replace with actual endpoint

        HttpRequest request = HttpRequest.newBuilder()
                .uri(uri)
                .header("Accept", "application/json")
                .GET()
                .build();

        HttpResponse<String> response = httpClient.send(request, HttpResponse.BodyHandlers.ofString());
        
        if (response.statusCode() == 200) {
            return response.body(); // Assume response contains the balance list
        } else {
            throw new RuntimeException("Failed to retrieve balance list: " + response.body());
        }
    }
}
import org.springframework.web.bind.annotation.*;
import org.springframework.beans.factory.annotation.Autowired;

@RestController
@RequestMapping("/operator")
public class OperatorController {

    @Autowired
    private AOMService aomService;

    @PostMapping("/subscriber")
    public String createSubscriber(@RequestBody Map<String, String> subscriberDetails) {
        try {
            return aomService.createSubscriber(subscriberDetails);
        } catch (Exception e) {
            throw new ResponseStatusException(HttpStatus.INTERNAL_SERVER_ERROR, "Unable to create subscriber", e);
        }
    }

    @GetMapping("/subscriber/{id}/balance")
    public String getRemainingBalanceList(@PathVariable String id) {
        try {
            return aomService.getRemainingBalanceList(id);
        } catch (Exception e) {
            throw new ResponseStatusException(HttpStatus.INTERNAL_SERVER_ERROR, "Unable to retrieve balance list", e);
        }
    }
}
mvn clean package
