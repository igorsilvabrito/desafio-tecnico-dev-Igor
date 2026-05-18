# CampusFlow

![Status](https://img.shields.io/badge/status-em%20desenvolvimento-yellow)

Sistema para organização de eventos universitários com inscrições online, controle de presença via QR Code e gerenciamento de vagas.

---

## 1. Visão Geral e a Dor

Muitos eventos universitários ainda são organizados utilizando formulários, planilhas e listas de presença manuais. Esse processo dificulta o controle de participantes, gera problemas com limite de vagas e torna o gerenciamento do evento mais trabalhoso.

O CampusFlow foi criado para simplificar a organização desses eventos, centralizando inscrições, presença e acompanhamento dos participantes em uma única plataforma.

### O que está sendo resolvido?

- Inscrições desorganizadas
- Controle manual de presença
- Falta de controle de vagas
- Dificuldade em acompanhar participantes
- Processo manual de emissão de certificados

### Quem sofre com esse problema?

- Centros acadêmicos
- Empresas juniores
- Organizadores de palestras e workshops
- Universidades
- Participantes dos eventos

### Por que isso importa?

A organização manual gera perda de tempo, erros no controle de participantes, dificuldade operacional e experiência ruim para os usuários. O CampusFlow automatiza esses processos e melhora a organização geral.

---

## 2. Arquitetura e Decisões Técnicas

| Camada | Escolha | Motivo | Alternativa considerada | Nota de impacto |
|--------|---------|--------|------------------------|-----------------|
| Front-end | React + TypeScript | Interfaces modernas e componentizadas com tipagem segura | Vue.js | Escalabilidade e manutenção |
| UI | Tailwind CSS | Desenvolvimento rápido sem sair do HTML | Bootstrap | Time-to-market |
| Back-end | Spring Boot | Estrutura robusta para APIs REST com ecossistema maduro | Node.js | Segurança e organização |
| Banco de dados | PostgreSQL | Relacionamentos complexos entre eventos, inscrições e usuários | MySQL | Consistência e queries |
| API | REST | Comunicação simples e bem documentada entre front e back | GraphQL | Integração e versionamento |
| Autenticação | JWT | Stateless, sem necessidade de sessão no servidor | Sessions | Performance e escalabilidade |

---

## 3. Demonstração

- **Demo:** [LINK DO DEPLOY]
- **Vídeo:** [LINK DO VÍDEO]

### Fluxo principal (15 segundos)

1. Organizador cria um evento
2. Usuário realiza inscrição
3. Sistema gera QR Code
4. Organizador faz check-in do participante
5. Participação é registrada

---

## 4. Destaque de Engenharia / "The Hard Part"

Um dos principais desafios foi controlar o limite de vagas do evento para impedir inscrições acima da capacidade definida — especialmente em cenários de acesso simultâneo.

A solução foi usar `@Transactional` para garantir que a verificação e o decremento de vagas aconteçam de forma atômica, evitando condições de corrida.

```java
@Transactional
public Enrollment enroll(Long userId, Long eventId) {

    // Busca o evento ou lança exceção se não existir
    Event event = eventRepository.findById(eventId)
        .orElseThrow(() -> new EventNotFoundException(eventId));

    // Verifica disponibilidade antes de prosseguir
    if (event.getAvailableSpots() <= 0) {
        throw new EventFullException("Evento sem vagas disponíveis");
    }

    // Cria e persiste a inscrição
    Enrollment enrollment = new Enrollment(userId, eventId);
    enrollmentRepository.save(enrollment);

    // Decrementa vagas de forma atômica com a transação
    event.setAvailableSpots(event.getAvailableSpots() - 1);
    eventRepository.save(event);

    return enrollment;
}
```

**Impacto:** controle correto das vagas, sem inscrições acima do limite e com dados sempre consistentes.

---

## 5. Insights e Valor de Negócio

**Para produto:**
- Facilita organização dos eventos e reduz trabalho manual
- Melhora experiência dos participantes com fluxo digital

**Para negócio:**
- Melhor controle operacional e organização mais profissional
- Facilidade para acompanhar e replicar eventos

**Para dados:**
O sistema pode gerar informações como quantidade de inscritos por evento, taxa de presença, eventos mais populares e perfil dos participantes — base para decisões futuras de produto.

---

## 6. Instalação e Uso

```bash
# 1. Clone o projeto
git clone https://github.com/seu-usuario/campusflow.git
cd campusflow

# 2. Execute os containers
docker compose up --build

# 3. Front-end (sem Docker)
npm install
npm run dev

# 4. Back-end (sem Docker)
./mvnw spring-boot:run
```

### Uso

Abra: `http://localhost:5173`

**Fluxo de teste:**
1. Criar conta
2. Criar evento
3. Fazer inscrição
4. Gerar QR Code
5. Fazer check-in

---

## 7. Roadmap

- [ ] Dashboard simples de métricas
- [ ] Exportação de lista de participantes em CSV
- [ ] Upload de imagem para eventos
- [ ] Sistema de certificados automáticos
- [ ] Responsividade mobile

---

## 8. Considerações Finais

O CampusFlow resolve problemas reais da organização de eventos universitários, focando em praticidade e facilidade de uso. O projeto foi desenvolvido com arquitetura simples e de fácil manutenção, permitindo evoluções sem aumentar a complexidade desnecessariamente.
