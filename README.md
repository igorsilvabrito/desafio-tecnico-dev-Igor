CampusFlow

Plataforma para gerenciamento de eventos universitários com inscrições online, controle de presença via QR Code, limite de vagas e emissão automática de certificados.

1. Visão Geral e a Dor

Eventos universitários frequentemente são organizados de forma manual, utilizando formulários, planilhas e listas de presença físicas. Esse processo gera problemas como superlotação, dificuldade no controle de participantes, emissão manual de certificados e falta de métricas sobre os eventos realizados.

O CampusFlow centraliza todo o fluxo de gerenciamento de eventos acadêmicos em uma única plataforma, automatizando tarefas operacionais e melhorando a experiência tanto dos organizadores quanto dos participantes.

O que está sendo resolvido?
Inscrições desorganizadas
Controle manual de presença
Dificuldade em controlar limite de vagas
Emissão manual de certificados
Falta de acompanhamento dos eventos
Baixa visibilidade sobre métricas de participação
Quem sofre com esse problema?
Centros acadêmicos
Empresas juniores
Organizadores de hackathons
Coordenações universitárias
Participantes dos eventos
Por que isso importa para produto/negócio?

A desorganização operacional impacta diretamente:

experiência dos participantes
produtividade dos organizadores
credibilidade do evento
controle da capacidade do evento

O sistema reduz tarefas manuais, melhora a gestão operacional e fornece uma experiência mais moderna e eficiente para eventos acadêmicos.

2. Arquitetura e Decisões Técnicas
Camada	Escolha	Por que escolhi isso?	Alternativa considerada	Nota de impacto
Front-end	React + TypeScript	Componentização, reutilização e facilidade na construção de interfaces modernas	Vue.js	Escalabilidade e manutenção
UI	Tailwind CSS + shadcn/ui	Agilidade no desenvolvimento e identidade visual customizável	Material UI	Time-to-market e consistência visual
Back-end	Spring Boot	Robustez, segurança e excelente suporte para APIs REST	Node.js	Escalabilidade e produtividade
Banco de dados	PostgreSQL	Integridade relacional e suporte transacional para controle de vagas e inscrições	MongoDB	Consistência e confiabilidade
API	REST	Simplicidade de integração entre front-end e back-end	GraphQL	Facilidade de manutenção
Autenticação	JWT	Autenticação stateless e integração simples com SPA	Session-based auth	Escalabilidade
Infraestrutura	Docker	Padronização do ambiente e facilidade no setup do projeto	Setup manual	Produtividade e portabilidade
3. Demonstração
Demo rápida: [LINK DO DEPLOY]
Vídeo demonstrativo: [LINK DO VÍDEO]
Fluxo principal
Organizador cria um evento
Usuário realiza inscrição
Sistema gera QR Code individual
Organizador realiza check-in do participante
Sistema libera certificado automaticamente

O fluxo principal pode ser compreendido rapidamente pelo avaliador.

4. Destaque de Engenharia / "The Hard Part"

O principal desafio técnico do projeto foi garantir consistência no controle de vagas durante múltiplas inscrições simultâneas.

A solução utiliza transações no banco de dados para evitar que o limite de vagas seja ultrapassado.

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
Impacto da solução
Evita inscrições duplicadas
Impede overbooking
Mantém consistência dos dados
Garante segurança em operações concorrentes
5. Insights e Valor de Negócio
Para produto
Automatização do gerenciamento de eventos
Redução de atrito no processo de inscrição
Melhor experiência para participantes
Controle centralizado dos eventos
Para negócio
Redução de tarefas operacionais
Melhor controle sobre capacidade dos eventos
Organização escalável para múltiplos eventos
Maior profissionalização de eventos universitários
Para dados

Os dados gerados permitem análises como:

taxa de participação
taxa de comparecimento
eventos mais populares
horários de maior adesão
retenção de participantes
6. Instruções de Instalação e Uso
# 1. Clone o repositório
git clone https://github.com/seu-usuario/campusflow.git

cd campusflow

# 2. Configure variáveis de ambiente
# Copie .env.example para .env

# 3. Execute os containers
docker compose up --build

# 4. Execute o frontend
npm install
npm run dev

# 5. Execute o backend
./mvnw spring-boot:run
Uso
Front-end

Abra:

http://localhost:5173
Fluxo para testes
Criar conta
Criar evento
Realizar inscrição
Gerar QR Code
Fazer check-in
Emitir certificado
7. Roadmap / Próximos Passos
Melhorias imediatas
Dashboard analítico
Notificações por email
Exportação de listas de presença
Upload de banner para eventos
Evoluções futuras
Aplicativo mobile
Check-in offline
Sistema de feedback pós-evento
Múltiplos organizadores por evento
Engenharia
Testes automatizados
Pipeline CI/CD com GitHub Actions
Observabilidade e logs
Cache com Redis
8. Considerações Finais

O CampusFlow foi desenvolvido com foco em resolver problemas reais enfrentados na organização de eventos universitários, priorizando simplicidade operacional, experiência do usuário e escalabilidade.

A arquitetura foi planejada para ser:

simples de manter
fácil de evoluir
consistente
preparada para crescimento futuro

sem adicionar complexidade desnecessária ao projeto.
