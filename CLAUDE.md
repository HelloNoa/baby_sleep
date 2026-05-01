# CLAUDE.md

이 파일은 Claude가 프로젝트를 이해하는 데 필요한 컨텍스트를 제공합니다.

## 프로젝트 개요

온라인 게이밍 플랫폼의 백엔드 API 서버입니다. 사용자(v1), 관리자/오피스(v2), 대리점(v3) 세 가지 버전의 API를 제공합니다.

## 기술 스택

- **프레임워크**: NestJS 10 + Express
- **언어**: TypeScript 5.1
- **데이터베이스**: PostgreSQL 16 + Drizzle ORM
- **캐시/세션**: Redis + IORedis
- **실시간**: Socket.io
- **큐**: BullMQ
- **인증**: Express-session (Redis 기반), Speakeasy (MFA)
- **검증**: class-validator, class-transformer
- **문서화**: Swagger/OpenAPI

## 프로젝트 구조

```
src/
├── domain/              # 비즈니스 로직 (버전별 구분)
│   ├── v1/             # 사용자 API (게임, 베팅, 입출금 등)
│   ├── v2/             # 관리자 API (사이트 관리, 회원 관리 등)
│   ├── v3/             # 대리점 API
│   └── callback/       # 게임 콜백 처리
├── global/providers/   # DB Provider (Drizzle)
├── middleware/         # API 키 검증, 인증 가드, 스로틀링
├── socket/             # Socket.io 게이트웨이
├── redis/              # Redis 서비스
├── cronjob/            # 스케줄 작업
├── constant/           # 설정값, 응답 코드
├── util/               # 공용 유틸리티
└── types/              # TypeScript 인터페이스
drizzle/                # DB 스키마 및 마이그레이션
```

## 주요 명령어

```bash
# 개발
pnpm start:dev         # 개발 서버 (watch 모드)
pnpm build            # 빌드
pnpm start:prod       # 프로덕션 실행

# 코드 품질
pnpm lint             # ESLint
pnpm format           # Prettier
pnpm test             # Jest 테스트

# 데이터베이스
pnpm generate         # Drizzle 마이그레이션 생성
pnpm introspect       # DB 스키마 인트로스펙션
```

## 코딩 컨벤션

### 파일/디렉토리 구조
각 모듈은 다음 패턴을 따릅니다:
```
domain/v{N}/{feature}/
├── {feature}.module.ts      # NestJS 모듈
├── {feature}.controller.ts  # HTTP 엔드포인트
├── {feature}.service.ts     # 비즈니스 로직
├── {feature}.dto.ts         # DTO (검증 포함)
└── {feature}.queue.ts       # 비동기 작업 (선택)
```

### 네이밍 규칙
- **파일명**: kebab-case (`account.service.ts`)
- **클래스**: PascalCase + 접미사 (`AccountService`, `V1SessionAuthGuard`)
- **DTO**: PascalCase + `Dto` (`V1AccountUserSignInDto`)
- **함수/메서드**: camelCase (`getSessionData`)
- **상수**: UPPER_SNAKE_CASE (`VERIFICATION_CODE_EXPIRE_TIME`)
- **버전 접두사**: `V1`, `V2`, `V3` (클래스/서비스명에 사용)

### DTO 패턴
```typescript
export class CreateUserDto {
  @ApiProperty({ example: 'user@example.com', description: '로그인 ID' })
  @IsString()
  @IsNotEmpty()
  loginId: string;

  @ApiPropertyOptional({ example: 'Asia/Seoul' })
  @IsOptional()
  @IsString()
  timezone?: string;
}
```

### 컨트롤러 패턴
```typescript
@Controller('v1/account')
@ApiTags('V1 Account')
export class AccountController {
  constructor(private readonly accountService: AccountService) {}

  @Post('signin')
  @ApiOperation({ summary: '로그인' })
  @UseGuards(V1SessionAuthGuard)
  async signIn(@Body() dto: SignInDto, @Session() session: SessionData) {
    return this.accountService.signIn(dto, session);
  }
}
```

### 서비스 패턴
```typescript
@Injectable()
export class AccountService {
  constructor(
    @Inject(DB) private readonly db: DbType,
    private readonly redisService: RedisService,
  ) {}
}
```

## API 버전별 특징

| 버전 | 용도 | 인증 방식 | 세션 쿠키 |
|-----|------|---------|----------|
| v1 | 사용자 | 세션 + API 키 | `user.sid` |
| v2 | 관리자 | 세션 + Bearer 토큰 | `office.sid` |
| v3 | 대리점 | 세션 + Bearer 토큰 | `agency.sid` |

## 데이터베이스 쿼리 패턴

Drizzle ORM 사용:
```typescript
// 기본 조회
const users = await this.db.select().from(user).where(eq(user.id, id));

// 복잡한 쿼리
const result = await this.db
  .select({ count: sql<number>`count(*)` })
  .from(betting)
  .where(and(eq(betting.userId, userId), gte(betting.createdAt, startDate)));
```

## 세션/Redis 키 규칙

- 세션: `sess:v1:`, `sess:v2:`, `sess:v3:`
- 소켓 룸: `socket:livechat:rooms:`
- 캐시: 기능별 prefix 사용

## 환경 변수 (.env)

필수 환경 변수:
- `DATABASE_URL`: PostgreSQL 연결 문자열
- `REDIS_HOST`, `REDIS_PORT`: Redis 설정
- `SESSION_SECRET_V1`, `SESSION_SECRET_V2`, `SESSION_SECRET_V3`: 세션 시크릿
- `PORT`: 서버 포트 (기본 10001)

## 주의사항

- TypeScript 설정에서 `strictNullChecks: false`, `noImplicitAny: false` 사용 중
- 서비스 파일이 800-1000줄 이상으로 큰 편
- 멀티 테넌시 지원 (`siteId`로 구분)
- 커밋 메시지는 Commitlint 규칙 준수 필요
- **NestJS 라우트 선언 순서**: 컨트롤러에서 고정 경로(`list`, `setting`, `system-betting` 등)는 반드시 와일드카드 경로(`:id`, `:siteId`)보다 **위에** 선언해야 함. 그렇지 않으면 고정 경로가 와일드카드 파라미터로 매칭되어 잘못된 핸들러/DTO 검증이 실행됨

## Git 커밋 규칙

- 커밋 메시지에 `Co-Authored-By: Claude` 또는 Claude Code 관련 링크를 포함하지 말 것
- 간결하고 명확한 커밋 메시지 작성 (conventional commits 형식)