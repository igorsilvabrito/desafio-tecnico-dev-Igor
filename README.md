# CampusFlow

![Status](https://img.shields.io/badge/status-em%20desenvolvimento-yellow)

Plataforma para gerenciamento de eventos universitários com inscrições online, controle de presença via QR Code, limite de vagas e emissão automática de certificados.

---

# 1. Visão Geral e a Dor

Eventos universitários frequentemente são organizados de forma manual, utilizando formulários, planilhas e listas de presença físicas. Esse processo gera problemas como superlotação, dificuldade no controle de participantes, emissão manual de certificados e falta de métricas sobre os eventos realizados.

O CampusFlow centraliza todo o fluxo de gerenciamento de eventos acadêmicos em uma única plataforma, automatizando tarefas operacionais e melhorando a experiência tanto dos organizadores quanto dos participantes.

## O que está sendo resolvido?

- Inscrições desorganizadas
- Controle manual de presença
- Dificuldade em controlar limite de vagas
- Emissão manual de certificados
- Falta de acompanhamento dos eventos
- Baixa visibilidade sobre métricas de participação

---

## Quem sofre com esse problema?

- Centros acadêmicos
- Empresas juniores
- Organizadores de hackathons
- Coordenações universitárias
- Participantes dos eventos

---

## Por que isso importa para produto/negócio?

A desorganização operacional impacta diretamente:
- experiência dos participantes
- produtividade dos organizadores
- credibilidade do evento
- controle da capacidade do evento

O sistema reduz tarefas manuais, melhora a gestão operacional e fornece uma experiência mais moderna e eficiente para eventos acadêmicos.

---

# 2. Arquitetura e Decisões Técnicas

| Camada | Escolha | Por que escolhi isso? | Alternativa considerada | Nota de impacto |
|---|---|---|---|---|
| Front-end | React + TypeScript | Componentização, reutilização e facilidade na construção de interfaces modernas | Vue.js | Escalabilidade e manutenção |
| UI | Tailwind CSS + shadcn/ui | Agilidade no desenvolvimento e identidade visual customizável | Material UI | Time-to-market e consistência visual |
| Back-end | Spring Boot | Robustez, segurança e excelente suporte para APIs REST | Node.js | Escalabilidade e produtividade |
| Banco de dados | PostgreSQL | Integridade relacional e suporte transacional para controle de vagas e inscrições | MongoDB | Consistência e confiabilidade |
| API | REST | Simplicidade de integração entre front-end e back-end | GraphQL | Facilidade de manutenção |
| Autenticação | JWT | Autenticação stateless e integração simples com SPA | Session-based auth | Escalabilidade |
| Infraestrutura | Docker | Padronização do ambiente e facilidade no setup do projeto | Setup manual | Produtividade e portabilidade |

---

# 3. Demonstração

- Demo rápida: [LINK DO DEPLOY]
- Vídeo demonstrativo: [LINK DO VÍDEO]

## Fluxo principal

1. Organizador cria um evento
2. Usuário realiza inscrição
3. Sistema gera QR Code individual
4. Organizador realiza check-in do participante
5. Sistema libera certificado automaticamente

O fluxo principal pode ser compreendido rapidamente pelo avaliador.

---

# 4. Destaque de Engenharia / "The Hard Part"

O principal desafio técnico do projeto foi garantir consistência no controle de vagas durante múltiplas inscrições simultâneas.

A solução utiliza transações no banco de dados para evitar que o limite de vagas seja ultrapassado.

```java
@Transactional
public EnrollmentResponse enrollUser(Long eventId, Long userId) {

    Event event = eventRepository.findById(eventId)
        .orElseThrow(() -> new RuntimeException("Evento não encontrado"));

    if (event.getAvailableSpots() <= 0) {
        throw new RuntimeException("Não há vagas disponíveis");
    }

    boolean alreadyEnrolled = enrollmentRepository
        .existsByUserIdAndEventId(userId, eventId);

    if (alreadyEnrolled) {
        throw new RuntimeException("Usuário já inscrito");
    }

    Enrollment enrollment = new Enrollment(userId, eventId);

    enrollmentRepository.save(enrollment);

    event.decreaseAvailableSpots();

    eventRepository.save(event);

    return new EnrollmentResponse("Inscrição realizada com sucesso");
}
