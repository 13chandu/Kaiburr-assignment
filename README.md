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
#task1 2part 
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

@RestController
@RequestMapping("/servers")
public class ServerController {
    private final Map<String, Server> serverStore = new ConcurrentHashMap<>();

    @GetMapping("/{id}")
    public Server getServer(@PathVariable String id) {
        return serverStore.get(id);
    }

    @GetMapping
    public List<Server> getAllServers() {
        return new ArrayList<>(serverStore.values());
    }

    @PostMapping
    public Server createServer(@RequestBody Server server) {
        serverStore.put(server.getId(), server);
        return server;
    }

    @PutMapping("/{id}")
    public Server updateServer(@PathVariable String id, @RequestBody Server server) {
        serverStore.put(id, server);
        return server;
    }

    @DeleteMapping("/{id}")
    public void deleteServer(@PathVariable String id) {
        serverStore.remove(id);
    }
}
#part 3
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;
import java.util.Optional;

@RestController
@RequestMapping("/servers")
public class ServerController {
    @Autowired
    private ServerRepository serverRepository;

    @GetMapping
    public List<Server> getAllServers() {
        return serverRepository.findAll();
    }

    @GetMapping("/{id}")
    public ResponseEntity<Server> getServerById(@PathVariable Long id) {
        Optional<Server> server = serverRepository.findById(id);
        return server.map(value -> new ResponseEntity<>(value, HttpStatus.OK))
                .orElseGet(() -> new ResponseEntity<>(HttpStatus.NOT_FOUND));
    }

    @PostMapping
    public ResponseEntity<Server> createServer(@RequestBody Server server) {
        Server savedServer = serverRepository.save(server);
        return new ResponseEntity<>(savedServer, HttpStatus.CREATED);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteServer(@PathVariable Long id) {
        serverRepository.deleteById(id);
        return new ResponseEntity<>(HttpStatus.NO_CONTENT);
    }
}

