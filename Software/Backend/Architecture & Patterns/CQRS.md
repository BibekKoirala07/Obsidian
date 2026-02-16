Here's your complete CQRS + Event Sourcing learning structure:

## CQRS + EVENT SOURCING - Complete Learning Guide

---

## 1. Core Concepts & Theory

### 1.1 What is CQRS?

- **Command Query Responsibility Segregation**
- Separate models for reading and writing data
- Commands: Change state (Create, Update, Delete)
- Queries: Read state (Get, List, Search)

**Questions to answer:**

- Why separate read and write models?
- What problems does CQRS solve?
- When should you NOT use CQRS?
- How is CQRS different from traditional CRUD?

### 1.2 Traditional Architecture Problems

- Single model for reads and writes
- Complex queries slow down writes
- Difficult to scale reads independently
- Hard to optimize for different access patterns
- No audit trail

**Questions to answer:**

- Why can't you just add read replicas to solve scaling?
- What happens when 90% of traffic is reads?
- How do you audit changes in traditional CRUD?

### 1.3 CQRS Benefits & Trade-offs

**Benefits:**

- Independent scaling of reads/writes
- Optimized data models for different needs
- Better performance
- Complete audit trail (with Event Sourcing)
- Easier to understand business logic

**Trade-offs:**

- Increased complexity
- Eventual consistency
- More code to maintain
- Learning curve
- Infrastructure overhead

**Questions to answer:**

- Is CQRS worth it for simple CRUD apps?
- How do you handle eventual consistency in UI?
- What's the minimum team size to handle CQRS complexity?

---

## 2. Event Sourcing Fundamentals

### 2.1 What is Event Sourcing?

- Store **events** (what happened) instead of **current state**
- Events are immutable, append-only
- Current state = replay all events
- Complete history of all changes

**Questions to answer:**

- What's the difference between Event Sourcing and event-driven architecture?
- Can you have CQRS without Event Sourcing?
- Can you have Event Sourcing without CQRS?
- Why are events immutable?

### 2.2 Events vs Commands vs State

**Command:**

- Intent to do something: `CreatePayment`, `CancelOrder`
- Can be rejected
- Present/future tense
- Validation happens here

**Event:**

- Something that happened: `PaymentCreated`, `OrderCancelled`
- Already happened, cannot be rejected
- Past tense
- Immutable, factual

**State:**

- Current snapshot: `{ balance: 1000, status: 'active' }`
- Derived from events
- Can be reconstructed at any time

**Questions to answer:**

- Why use past tense for events?
- Can an event fail?
- What happens if command validation fails?
- How do you handle events that were recorded incorrectly?

### 2.3 Event Store

- Specialized database for events
- Append-only log
- Optimized for sequential writes
- Can query by aggregate ID, event type, timestamp

**Questions to answer:**

- Can you use MongoDB/PostgreSQL as event store?
- What's the difference between event store and message broker?
- How do you query event store efficiently?
- What happens when event store grows to billions of events?

---

## 3. CQRS Architecture Components

### 3.1 Command Side (Write Model)

**Command:**

typescript

```typescript
class CreatePaymentCommand {
  userId: string;
  amount: number;
  currency: string;
}
```

**Command Handler:**

- Validates command
- Loads aggregate
- Executes business logic
- Saves events

**Aggregate:**

- Domain entity that ensures consistency
- Loads events, applies business rules
- Emits new events

**Questions to answer:**

- Where does validation happen?
- What's the difference between aggregate and entity?
- Can one command affect multiple aggregates?
- How do you handle commands that need data from query side?

### 3.2 Query Side (Read Model)

**Read Model:**

- Optimized for queries
- Denormalized data
- Can have multiple read models for same data
- Eventually consistent with write model

**Projection/View:**

- Listens to events
- Updates read model
- Can rebuild from event history

**Query Handler:**

- Simple data retrieval
- No business logic
- Returns DTOs

**Questions to answer:**

- Can you have multiple read models for same domain?
- How do you rebuild read models?
- What happens if read model gets corrupted?
- How do you handle queries that span multiple aggregates?

### 3.3 Event Bus

- Connects write side to read side
- Publishes events after command succeeds
- RabbitMQ, Kafka, or in-memory

**Questions to answer:**

- What happens if event bus is down?
- How do you ensure events are delivered?
- Can you lose events between write and read side?

---

## 4. Aggregates & Domain-Driven Design

### 4.1 Aggregate Root

- Entry point for all operations
- Enforces business invariants
- Has unique ID
- Produces events

**Example: Payment Aggregate**

typescript

```typescript
class Payment {
  id: string;
  status: 'pending' | 'completed' | 'failed';
  amount: number;
  
  // Business logic
  process() {
    if (this.status !== 'pending') {
      throw new Error('Cannot process non-pending payment');
    }
    this.apply(new PaymentProcessedEvent(...));
  }
}
```

**Questions to answer:**

- What's the difference between aggregate and entity?
- Can aggregates reference other aggregates?
- How big should an aggregate be?
- What happens if business rule spans multiple aggregates?

### 4.2 Aggregate Boundaries

- Transactional boundary
- Consistency boundary
- One aggregate per transaction

**Questions to answer:**

- How do you identify aggregate boundaries?
- What if you need to update 2 aggregates atomically?
- How do you handle cross-aggregate validation?

### 4.3 Aggregate Lifecycle

1. **Load aggregate:** Replay events from event store
2. **Execute command:** Apply business logic
3. **Generate events:** What changed
4. **Persist events:** Save to event store
5. **Publish events:** Notify read side

**Questions to answer:**

- Do you load ALL events every time? (Hint: Snapshots)
- What if there are 1 million events for one aggregate?
- How do you optimize aggregate loading?

---

## 5. Event Design & Versioning

### 5.1 Event Structure

typescript

```typescript
interface DomainEvent {
  eventId: string;          // UUID
  aggregateId: string;      // Which aggregate
  aggregateType: string;    // 'Payment', 'Order'
  eventType: string;        // 'PaymentCreated'
  eventVersion: number;     // Schema version
  timestamp: Date;
  userId: string;           // Who did it
  data: any;                // Event payload
  metadata?: any;           // Correlation, causation IDs
}
```

### 5.2 Event Naming Conventions

- Past tense: `PaymentCreated`, not `CreatePayment`
- Domain language: `PaymentAuthorized`, not `PaymentUpdated`
- Specific: `PaymentDeclinedDueToInsufficientFunds`, not `PaymentFailed`

**Questions to answer:**

- Why be specific in event names?
- How do generic events make debugging harder?

### 5.3 Event Versioning (CRITICAL)

- Events are immutable, but schemas evolve
- Version 1: `{ amount: 100 }`
- Version 2: `{ amount: 100, currency: 'USD' }`

**Strategies:**

- **Upcasting:** Convert old events to new format when loading
- **Multiple handlers:** Support both versions
- **Weak schema:** Use optional fields

**Questions to answer:**

- Can you change an event after it's saved?
- How do you handle breaking changes?
- What if you need to add required field to existing events?
- When do you upcast: at read time or batch migration?

### 5.4 Event Granularity

- **Fine-grained:** `AmountChanged`, `StatusChanged`
- **Coarse-grained:** `PaymentUpdated` with all changes

**Questions to answer:**

- Which is better for audit trail?
- Which is better for performance?
- How do you decide granularity?

---

## 6. Snapshotting & Performance

### 6.1 The Problem

- Aggregate with 100,000 events takes forever to load
- Replay is expensive

### 6.2 Snapshot Strategy

- Store current state periodically
- Load snapshot + replay events after snapshot
- Example: Snapshot every 100 events

typescript

```typescript
interface Snapshot {
  aggregateId: string;
  version: number;          // Last event version in snapshot
  state: any;               // Current aggregate state
  timestamp: Date;
}
```

**Questions to answer:**

- How often should you snapshot?
- What if snapshot is corrupted?
- Do you delete events after snapshot?
- How do you rebuild from snapshot?

### 6.3 Optimization Techniques

- **Caching:** Keep frequently accessed aggregates in memory
- **CQRS:** Read models don't replay events
- **Event compression:** Archive old events
- **Partitioning:** Separate hot and cold data

**Questions to answer:**

- When do you invalidate cache?
- How do you handle stale cache?
- Can you delete old events?

---

## 7. Read Model Projections

### 7.1 What is a Projection?

- Event handler that builds read model
- Listens to events, updates database
- Can have multiple projections from same events

**Example:**

typescript

```typescript
@EventsHandler(PaymentCreatedEvent)
class PaymentListProjection {
  async handle(event: PaymentCreatedEvent) {
    await this.db.payments.insert({
      id: event.aggregateId,
      amount: event.data.amount,
      status: 'pending',
      createdAt: event.timestamp
    });
  }
}
```

### 7.2 Multiple Read Models

- **List view:** Optimized for listing (denormalized)
- **Detail view:** Optimized for single item
- **Analytics:** Aggregated statistics
- **Search:** ElasticSearch index

**Questions to answer:**

- Why have multiple read models?
- Can they use different databases?
- How do you keep them in sync?

### 7.3 Rebuilding Projections

- Replay all events from event store
- Useful when: schema changes, bugs fixed, new projection added

**Process:**

1. Create new read model table
2. Replay events from beginning
3. Switch to new table
4. Delete old table

**Questions to answer:**

- How do you rebuild without downtime?
- What if events take hours to replay?
- How do you handle errors during rebuild?

### 7.4 Projection Patterns

**Inline Projection:**

- Update read model in same transaction as event save
- Pros: Immediate consistency
- Cons: Slower writes, coupling

**Async Projection:**

- Update via message broker
- Pros: Fast writes, decoupled
- Cons: Eventual consistency

**Questions to answer:**

- Which pattern for critical data?
- How do you handle projection failures?
- What's acceptable lag for read models?

---

## 8. Eventual Consistency

### 8.1 The Challenge

- Write completes immediately
- Read model updates later (milliseconds to seconds)
- User might not see their changes immediately

**Questions to answer:**

- How do you explain this to users?
- What UIs hide eventual consistency?
- When is eventual consistency unacceptable?

### 8.2 Handling in UI

**Optimistic UI:**

- Show change immediately
- Revert if event processing fails

**Loading States:**

- Show "Processing..." until read model updates

**Polling:**

- Poll read model until updated

**WebSockets:**

- Push update when read model ready

**Questions to answer:**

- Which approach for payment confirmation?
- How long is acceptable wait?
- What if read model never updates?

### 8.3 Versioning in Queries

- Track version number in read model
- Client sends version with command
- Reject if versions don't match (optimistic locking)

**Questions to answer:**

- How do you prevent lost updates?
- What's version in eventual consistency?

---

## 9. Error Handling & Compensating Transactions

### 9.1 Command Failures

- Validation fails: Reject before aggregate
- Business rule fails: Throw error in aggregate
- Technical failure: Retry or fail

**Questions to answer:**

- Where do you validate commands?
- How do you return errors to user?
- Do you retry failed commands?

### 9.2 Event Processing Failures

- Projection fails to update read model
- What to do?

**Strategies:**

- **Retry:** Try again (idempotent handlers required)
- **Dead letter queue:** Manual intervention
- **Skip:** Log and continue (dangerous)

**Questions to answer:**

- How many retries?
- What makes handler idempotent?
- When do you skip events?

### 9.3 Compensating Events

- Can't delete events
- Emit "reverse" event instead

**Example:**

- Event: `PaymentCreated { amount: 100 }`
- Compensating: `PaymentCancelled { amount: 100 }`

**Questions to answer:**

- Why not delete wrong event?
- How do you handle user mistakes?
- What's difference between compensation and correction?

### 9.4 Saga Pattern (Preview)

- Coordinate multiple aggregates
- If one fails, compensate others
- More in next section

---

## 10. NestJS CQRS Implementation

### 10.1 NestJS CQRS Package

bash

```bash
npm install @nestjs/cqrs
```

### 10.2 Command Structure

typescript

```typescript
// Command
export class CreatePaymentCommand {
  constructor(
    public readonly userId: string,
    public readonly amount: number,
    public readonly currency: string,
  ) {}
}

// Command Handler
@CommandHandler(CreatePaymentCommand)
export class CreatePaymentHandler implements ICommandHandler<CreatePaymentCommand> {
  constructor(
    private readonly eventStore: EventStore,
    private readonly eventBus: EventBus,
  ) {}

  async execute(command: CreatePaymentCommand) {
    // 1. Load aggregate (or create new)
    const payment = new Payment(command.userId, command.amount);
    
    // 2. Execute business logic
    payment.create();
    
    // 3. Persist events
    await this.eventStore.save(payment.getUncommittedEvents());
    
    // 4. Publish events
    payment.commit();
  }
}
```

### 10.3 Event Structure

typescript

```typescript
// Event
export class PaymentCreatedEvent {
  constructor(
    public readonly paymentId: string,
    public readonly userId: string,
    public readonly amount: number,
    public readonly currency: string,
    public readonly timestamp: Date,
  ) {}
}

// Event Handler (Projection)
@EventsHandler(PaymentCreatedEvent)
export class PaymentCreatedHandler implements IEventHandler<PaymentCreatedEvent> {
  constructor(private readonly readModel: PaymentReadModel) {}

  async handle(event: PaymentCreatedEvent) {
    await this.readModel.create({
      id: event.paymentId,
      userId: event.userId,
      amount: event.amount,
      currency: event.currency,
      status: 'pending',
      createdAt: event.timestamp,
    });
  }
}
```

### 10.4 Query Structure

typescript

```typescript
// Query
export class GetPaymentQuery {
  constructor(public readonly paymentId: string) {}
}

// Query Handler
@QueryHandler(GetPaymentQuery)
export class GetPaymentHandler implements IQueryHandler<GetPaymentQuery> {
  constructor(private readonly readModel: PaymentReadModel) {}

  async execute(query: GetPaymentQuery) {
    return this.readModel.findById(query.paymentId);
  }
}
```

### 10.5 Aggregate Root

typescript

```typescript
export class Payment extends AggregateRoot {
  private status: 'pending' | 'completed' | 'failed';
  private amount: number;
  
  constructor(private userId: string, amount: number) {
    super();
    this.amount = amount;
    this.status = 'pending';
  }
  
  create() {
    // Business validation
    if (this.amount <= 0) {
      throw new BadRequestException('Amount must be positive');
    }
    
    // Emit event
    this.apply(new PaymentCreatedEvent(
      this.id,
      this.userId,
      this.amount,
      'USD',
      new Date(),
    ));
  }
  
  process() {
    if (this.status !== 'pending') {
      throw new BadRequestException('Can only process pending payments');
    }
    
    this.apply(new PaymentProcessedEvent(this.id, new Date()));
  }
  
  // Apply events to update state
  onPaymentCreatedEvent(event: PaymentCreatedEvent) {
    this.id = event.paymentId;
    this.status = 'pending';
  }
  
  onPaymentProcessedEvent(event: PaymentProcessedEvent) {
    this.status = 'completed';
  }
}
```

### 10.6 Module Setup

typescript

```typescript
@Module({
  imports: [
    CqrsModule,
    MongooseModule.forFeature([{ name: 'Payment', schema: PaymentSchema }]),
  ],
  providers: [
    // Command Handlers
    CreatePaymentHandler,
    ProcessPaymentHandler,
    
    // Event Handlers (Projections)
    PaymentCreatedHandler,
    PaymentProcessedHandler,
    
    // Query Handlers
    GetPaymentHandler,
    ListPaymentsHandler,
    
    // Services
    PaymentReadModel,
    EventStore,
  ],
  controllers: [PaymentController],
})
export class PaymentModule {}
```

**Questions to answer:**

- Where does CommandBus come from?
- How does EventBus publish to RabbitMQ?
- Where do you register event handlers?

---

## 11. Event Store Implementation

### 11.1 Event Store Interface

typescript

```typescript
interface EventStore {
  save(aggregateId: string, events: DomainEvent[]): Promise<void>;
  load(aggregateId: string): Promise<DomainEvent[]>;
  loadFromVersion(aggregateId: string, version: number): Promise<DomainEvent[]>;
}
```

### 11.2 PostgreSQL Event Store

sql

```sql
CREATE TABLE events (
  event_id UUID PRIMARY KEY,
  aggregate_id UUID NOT NULL,
  aggregate_type VARCHAR(255) NOT NULL,
  event_type VARCHAR(255) NOT NULL,
  event_version INTEGER NOT NULL,
  event_data JSONB NOT NULL,
  metadata JSONB,
  timestamp TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  UNIQUE(aggregate_id, event_version)
);

CREATE INDEX idx_aggregate_id ON events(aggregate_id);
CREATE INDEX idx_event_type ON events(event_type);
CREATE INDEX idx_timestamp ON events(timestamp);
```

### 11.3 MongoDB Event Store

typescript

```typescript
@Schema()
export class Event {
  @Prop({ required: true })
  eventId: string;
  
  @Prop({ required: true, index: true })
  aggregateId: string;
  
  @Prop({ required: true })
  aggregateType: string;
  
  @Prop({ required: true, index: true })
  eventType: string;
  
  @Prop({ required: true })
  eventVersion: number;
  
  @Prop({ type: Object, required: true })
  eventData: any;
  
  @Prop({ type: Object })
  metadata?: any;
  
  @Prop({ required: true })
  timestamp: Date;
}
```

### 11.4 Specialized Event Stores

- **EventStoreDB:** Purpose-built for event sourcing
- **Apache Kafka:** Event streaming platform
- **AWS DynamoDB:** With streams for projections

**Questions to answer:**

- Can you use MongoDB for event store?
- What's difference between event store and message broker?
- Do you need specialized event store?

---

## 12. Integration with RabbitMQ

### 12.1 Event Publishing

typescript

```typescript
@EventsHandler(PaymentCreatedEvent)
export class PaymentEventPublisher implements IEventHandler<PaymentCreatedEvent> {
  constructor(
    @Inject('RABBITMQ_SERVICE') private rabbitMQ: ClientProxy,
  ) {}
  
  async handle(event: PaymentCreatedEvent) {
    // Publish to RabbitMQ for other services
    await this.rabbitMQ.emit('payment.created', event);
  }
}
```

### 12.2 Cross-Service Communication

- Service A: Emits event to event store
- Projection: Publishes to RabbitMQ
- Service B: Consumes from RabbitMQ, creates command

**Questions to answer:**

- Why not publish directly to RabbitMQ from aggregate?
- How do you ensure events reach both event store and RabbitMQ?
- What if RabbitMQ is down?

### 12.3 Event Bus vs Message Broker

- **Event Bus (internal):** Within same service, in-memory or local DB
- **Message Broker (external):** Between services, RabbitMQ/Kafka

**Questions to answer:**

- When do you use each?
- Can they be the same thing?

---

## 13. Testing CQRS

### 13.1 Testing Commands

typescript

```typescript
describe('CreatePaymentCommand', () => {
  it('should create payment when valid', async () => {
    const command = new CreatePaymentCommand('user1', 100, 'USD');
    const handler = new CreatePaymentHandler(eventStore, eventBus);
    
    await handler.execute(command);
    
    const events = await eventStore.load(payment.id);
    expect(events).toHaveLength(1);
    expect(events[0]).toBeInstanceOf(PaymentCreatedEvent);
  });
  
  it('should reject negative amount', async () => {
    const command = new CreatePaymentCommand('user1', -100, 'USD');
    await expect(handler.execute(command)).rejects.toThrow();
  });
});
```

### 13.2 Testing Aggregates

typescript

```typescript
describe('Payment Aggregate', () => {
  it('should not process already completed payment', () => {
    const payment = new Payment('user1', 100);
    payment.create();
    payment.process();
    
    expect(() => payment.process()).toThrow('Can only process pending');
  });
});
```

### 13.3 Testing Projections

typescript

```typescript
describe('PaymentCreatedHandler', () => {
  it('should create read model', async () => {
    const event = new PaymentCreatedEvent('p1', 'u1', 100, 'USD', new Date());
    const handler = new PaymentCreatedHandler(readModel);
    
    await handler.handle(event);
    
    const payment = await readModel.findById('p1');
    expect(payment.amount).toBe(100);
  });
});
```

### 13.4 Testing Event Replay

typescript

```typescript
describe('Event Replay', () => {
  it('should rebuild aggregate from events', async () => {
    const events = [
      new PaymentCreatedEvent('p1', 'u1', 100, 'USD', new Date()),
      new PaymentProcessedEvent('p1', new Date()),
    ];
    
    const payment = new Payment();
    events.forEach(e => payment.loadFromHistory(e));
    
    expect(payment.status).toBe('completed');
  });
});
```

---

## 14. Fintech-Specific Patterns

### 14.1 Transaction Ledger with Event Sourcing

typescript

```typescript
// Events
class AccountCreditedEvent {
  constructor(
    public accountId: string,
    public amount: number,
    public transactionId: string,
    public timestamp: Date,
  ) {}
}

class AccountDebitedEvent {
  constructor(
    public accountId: string,
    public amount: number,
    public transactionId: string,
    public timestamp: Date,
  ) {}
}

// Aggregate
class Account extends AggregateRoot {
  private balance: number = 0;
  
  credit(amount: number, transactionId: string) {
    if (amount <= 0) throw new Error('Amount must be positive');
    this.apply(new AccountCreditedEvent(this.id, amount, transactionId, new Date()));
  }
  
  debit(amount: number, transactionId: string) {
    if (amount <= 0) throw new Error('Amount must be positive');
    if (this.balance < amount) throw new Error('Insufficient funds');
    this.apply(new AccountDebitedEvent(this.id, amount, transactionId, new Date()));
  }
  
  onAccountCreditedEvent(event: AccountCreditedEvent) {
    this.balance += event.amount;
  }
  
  onAccountDebitedEvent(event: AccountDebitedEvent) {
    this.balance -= event.amount;
  }
}
```

### 14.2 Audit Trail

- Every change is an event
- Events never deleted
- Complete history for compliance

**Questions to answer:**

- How do you query "balance on specific date"?
- How do you prove transaction history to auditor?
- How do you handle GDPR right to deletion?

### 14.3 Idempotency with Event Sourcing

typescript

```typescript
class CreatePaymentCommand {
  constructor(
    public userId: string,
    public amount: number,
    public idempotencyKey: string, // Client-provided
  ) {}
}

@CommandHandler(CreatePaymentCommand)
class CreatePaymentHandler {
  async execute(command: CreatePaymentCommand) {
    // Check if already processed
    const existing = await this.eventStore.findByIdempotencyKey(
      command.idempotencyKey
    );
    if (existing) {
      return existing; // Return previous result
    }
    
    // Process command...
  }
}
```

### 14.4 Double-Entry Bookkeeping Events

typescript

```typescript
class TransferInitiatedEvent {
  constructor(
    public transferId: string,
    public fromAccountId: string,
    public toAccountId: string,
    public amount: number,
  ) {}
}

// Saga coordinates debit + credit
class TransferSaga {
  async handle(event: TransferInitiatedEvent) {
    // Debit from account
    await this.commandBus.execute(
      new DebitAccountCommand(event.fromAccountId, event.amount)
    );
    
    // Credit to account
    await this.commandBus.execute(
      new CreditAccountCommand(event.toAccountId, event.amount)
    );
  }
}
```

---

## 15. Advanced Topics

### 15.1 Temporal Queries

- Query state at specific point in time
- Replay events up to timestamp
- Useful for: compliance, dispute resolution

typescript

```typescript
async getBalanceAt(accountId: string, date: Date): Promise<number> {
  const events = await this.eventStore.loadUntil(accountId, date);
  const account = new Account();
  events.forEach(e => account.loadFromHistory(e));
  return account.balance;
}
```

### 15.2 Process Managers

- Coordinate multiple aggregates
- Long-running business processes
- Different from Saga (more complex)

### 15.3 Event Upcasting

typescript

```typescript
class EventUpcaster {
  upcast(event: any): DomainEvent {
    switch(event.eventVersion) {
      case 1:
        return this.upcastV1toV2(event);
      case 2:
        return event;
      default:
        throw new Error('Unknown version');
    }
  }
  
  private upcastV1toV2(event: any) {
    return {
      ...event,
      currency: 'USD', // Add missing field
      eventVersion: 2,
    };
  }
}
```

### 15.4 Blue-Green Deployment

- Run two versions simultaneously
- Route events to both
- Verify results match
- Switch traffic

**Questions to answer:**

- How do you deploy schema changes safely?
- What if new version has bugs?

---

## 16. Common Pitfalls & Anti-Patterns

### 16.1 Anti-Patterns (DON'T DO)

❌ **CRUD thinking in CQRS**

- Don't create `UpdatePayment` command
- Create specific commands: `ProcessPayment`, `CancelPayment`

❌ **Fat events**

- Don't include entire aggregate state
- Only include what changed

❌ **Using read model in command validation**

- Eventual consistency breaks this
- Load from event store instead

❌ **Too many aggregates**

- Start simple
- Don't make every entity an aggregate

❌ **Forgetting idempotency**

- Event handlers must be idempotent
- Same event processed twice = same result

❌ **No versioning strategy**

- Events are forever
- Plan for schema evolution

❌ **Synchronous projections**

- Blocks writes
- Use async projections

### 16.2 Best Practices (DO THIS)

✅ **Start simple**

- Don't apply CQRS everywhere
- Use for complex domains only

✅ **Domain-focused events**

- `PaymentDeclinedDueToInsufficientFunds` not `PaymentStatusChanged`

✅ **Small aggregates**

- One transaction = one aggregate
- Keep them focused

✅ **Idempotent handlers**

- Check if already processed
- Use event IDs for deduplication

✅ **Monitor lag**

- Track write-to-read delay
- Alert if projections fall behind

✅ **Test event replay**

- Regularly rebuild projections
- Ensures events are correct

✅ **Explicit commands**

- `ProcessPayment`, not `UpdatePayment`
- Reveals business intent

---

## 17. Monitoring & Observability

### 17.1 Key Metrics

**Command Side:**

- Command execution time
- Command failure rate
- Event store write latency
- Aggregate load time

**Query Side:**

- Projection lag (time behind event store)
- Read model query time
- Projection errors

**Questions to answer:**

- How do you know if projections are behind?
- What's acceptable lag?
- How do you alert on issues?

### 17.2 Logging

typescript

```typescript
@CommandHandler(CreatePaymentCommand)
class CreatePaymentHandler {
  async execute(command: CreatePaymentCommand) {
    const startTime = Date.now();
    
    this.logger.log(`Executing CreatePaymentCommand`, {
      userId: command.userId,
      amount: command.amount,
    });
    
    try {
      // Execute...
      
      this.logger.log(`Command succeeded in ${Date.now() - startTime}ms`);
    } catch (error) {
      this.logger.error(`Command failed: ${error.message}`);
      throw error;
    }
  }
}
```

### 17.3 Distributed Tracing

- Trace command → events → projections
- Use correlation ID
- OpenTelemetry integration

---

## 18. Validation Project: Digital Wallet System

**Build a complete digital wallet with CQRS + Event Sourcing:**

### Features:

1. **Create wallet** (command)
2. **Deposit funds** (command)
3. **Withdraw funds** (command)
4. **Transfer between wallets** (command + saga)
5. **Get wallet balance** (query)
6. **Get transaction history** (query from events)
7. **Get balance at specific date** (temporal query)

### Requirements:

**Command Side:**

- ✅ Wallet aggregate with business rules
- ✅ Commands: CreateWallet, Deposit, Withdraw, Transfer
- ✅ Events: WalletCreated, FundsDeposited, FundsWithdrawn, TransferInitiated, TransferCompleted
- ✅ Event store (PostgreSQL or MongoDB)
- ✅ Validation: no negative amounts, sufficient funds
- ✅ Idempotency keys for all commands

**Query Side:**

- ✅ Read model: current balances
- ✅ Read model: transaction list
- ✅ Projections update from events
- ✅ Queries: GetBalance, GetTransactionHistory

**Event Sourcing:**

- ✅ Store all events immut

Retry

This response paused because Claude reached its max length for a message. Hit continue to nudge Claude along.

Continue

[Claude can make mistakes.  
Please double-check responses.](https://support.anthropic.com/en/articles/8525154-claude-is-providing-incorrect-or-misleading-responses-what-s-going-on)

Session limit reached ∙ resets 3:45 AM

[Upgrade](/upgrade?hide_free=true)

  

Sonnet 4.5