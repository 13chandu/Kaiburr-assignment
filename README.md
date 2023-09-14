# Kaiburr-assignment
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

@RestController
@RequestMapping("/servers")
public class ServerController {

    private List<Server> servers = new ArrayList<>();
    private long nextId = 1;

    // GET /servers
    @GetMapping
    public ResponseEntity<List<Server>> getAllServers() {
        return ResponseEntity.ok(servers);
    }

    // GET /servers/{id}
    @GetMapping("/{id}")
    public ResponseEntity<Server> getServerById(@PathVariable Long id) {
        Optional<Server> server = servers.stream().filter(s -> s.getId().equals(id)).findFirst();
        return server.map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    // POST /servers
    @PostMapping
    public ResponseEntity<Server> createServer(@RequestBody Server server) {
        server.setId(nextId++);
        servers.add(server);
        return ResponseEntity.status(HttpStatus.CREATED).body(server);
    }

    // DELETE /servers/{id}
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteServer(@PathVariable Long id) {
        boolean removed = servers.removeIf(s -> s.getId().equals(id));
        if (removed) {
            return ResponseEntity.noContent().build();
        } else {
            return ResponseEntity.notFound().build();
        }
    }
}
