import java.util.List;
import java.util.Optional;

@SpringBootApplication
public class ServerApplication {


    public static void main(String[] args) {
        SpringApplication.run(ServerApplication.class, args);
    }
    
}

@Document(collection = "servers")
class Server {
    @Id
    private String id;
    private String name;
    private String language;
    private String framework;

    // Getters and setters

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getLanguage() {
        return language;
    }

    public void setLanguage(String language) {
        this.language = language;
    }

    public String getFramework() {
        return framework;
    }

    public void setFramework(String framework) {
        this.framework = framework;
    }
}

interface ServerRepository extends MongoRepository<Server, String> {
    List<Server> findByNameContaining(String name);

    List<Server> findAll();

    Optional<Server> findById(String id);

    Server save(Server server);

    void deleteById(String id);
}

@Service
class ServerService {
    @Autowired
    private ServerRepository serverRepository;

    public List<Server> getAllServers() {
        return serverRepository.findAll();
    }

    public Server getServerById(String id) {
        Optional<Server> server = serverRepository.findById(id);
        return server.orElse(null);
    }

    public Server createServer(Server server) {
        return serverRepository.save(server);
    }

    public void deleteServer(String id) {
        serverRepository.deleteById(id);
    }

    public List<Server> getServersByName(String name) {
        return serverRepository.findByNameContaining(name);
    }
}

@RestController
@RequestMapping("/servers")
class ServerController {
    @Autowired
    private ServerService serverService;

    @GetMapping(value = "")
    public List<Server> getAllServers() {
        return serverService.getAllServers();
    }

    @GetMapping("/{id}")
    public Server getServerById(@PathVariable String id) {
        return serverService.getServerById(id);
    }

    @PostMapping
    public Server createServer(@RequestBody Server server) {
        return serverService.createServer(server);
    }

    @DeleteMapping("/{id}")
    public void deleteServer(@PathVariable String id) {
        serverService.deleteServer(id);
    }

    @GetMapping("/find")
    public List<Server> getServersByName(@RequestParam String name) {
        return serverService.getServersByName(name);
    }
}
