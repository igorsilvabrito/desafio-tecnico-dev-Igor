# CampusFlow

![Status](https://img.shields.io/badge/status-em%20desenvolvimento-yellow)

Sistema para organização de eventos universitários com inscrições online, controle de presença via QR Code e gerenciamento de vagas.

---

# 1. Visão Geral e a Dor

Muitos eventos universitários ainda são organizados utilizando formulários, planilhas e listas de presença manuais. Esse processo dificulta o controle de participantes, gera problemas com limite de vagas e torna o gerenciamento do evento mais trabalhoso.

O CampusFlow foi criado para simplificar a organização desses eventos, centralizando inscrições, presença e acompanhamento dos participantes em uma única plataforma.

---

## O que está sendo resolvido?

- Inscrições desorganizadas
- Controle manual de presença
- Falta de controle de vagas
- Dificuldade em acompanhar participantes
- Processo manual de emissão de certificados

---

## Quem sofre com esse problema?

- Centros acadêmicos
- Empresas juniores
- Organizadores de palestras e workshops
- Universidades
- Participantes dos eventos

---

## Por que isso importa?

A organização manual gera:
- perda de tempo
- erros no controle de participantes
- dificuldade operacional
- experiência ruim para os usuários

O sistema automatiza processos simples do evento e melhora a organização geral.

---

# 2. Arquitetura e Decisões Técnicas

| Camada | Escolha | Motivo |
|---|---|---|
| Front-end | React + TypeScript | Facilidade na criação de interfaces modernas e componentizadas |
| UI | Tailwind CSS | Desenvolvimento rápido e estilização simples |
| Back-end | Spring Boot | Estrutura robusta para APIs REST |
| Banco de dados | PostgreSQL | Facilidade para trabalhar com relacionamentos e inscrições |
| API | REST | Comunicação simples entre front-end e back-end |
| Autenticação | JWT | Controle simples de autenticação |

---

# 3. Demonstração

- Demo: [LINK DO DEPLOY]
- Vídeo: [LINK DO VÍDEO]

## Fluxo principal

1. Organizador cria um evento
2. Usuário realiza inscrição
3. Sistema gera QR Code
4. Organizador faz check-in do participante
5. Participação é registrada

---

# 4. Destaque de Engenharia / "The Hard Part"

Um dos principais desafios foi controlar o limite de vagas do evento para impedir inscrições acima da capacidade definida.

```java
@Transactional
public Enrollment enroll(Long userId, Long eventId) {

    Event event = eventRepository.findById(eventId)
        .orElseThrow();

    if(event.getAvailableSpots() <= 0) {
        throw new RuntimeException("Evento lotado");
    }

    Enrollment enrollment = new Enrollment(userId, eventId);

    enrollmentRepository.save(enrollment);

    event.setAvailableSpots(
        event.getAvailableSpots() - 1
    );

    eventRepository.save(event);

    return enrollment;
}
## Resultado

- Controle correto das vagas
- Evita inscrições acima do limite
- Mantém os dados consistentes

---

# 5. Insights e Valor de Negócio

## Para produto

- Facilita organização dos eventos
- Reduz trabalho manual
- Melhora experiência dos participantes

---

## Para negócio

- Melhor controle operacional
- Organização mais profissional
- Facilidade para acompanhar eventos

---

## Para dados

O sistema pode gerar informações como:

- quantidade de inscritos
- presença nos eventos
- eventos mais populares

---

# 6. Instalação e Uso

```bash
# Clone o projeto
git clone https://github.com/seu-usuario/campusflow.git

cd campusflow

# Execute os containers
docker compose up --build

# Front-end
npm install
npm run dev

# Back-end
./mvnw spring-boot:run
